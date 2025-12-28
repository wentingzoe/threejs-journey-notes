# 0.9 Debug UI (lil-gui)

## Summary

- Add a lightweight debug panel so you (and designers/clients) can tweak parameters live—color, speed, visibility, geometry detail, etc.—without editing code and refreshing blindly.
- We use **lil-gui** (modern dat.GUI replacement), learn common control types (range / checkbox / color / button), organizing with folders, and important pitfalls: **GUI tweaks only work on object properties** and **dispose geometry** to avoid GPU memory leaks.

---

### Main

This chapter teaches you how to add a **debug UI** so you (and designers/clients) can **tweak parameters live**—color, speed, visibility, geometry detail, etc.—without editing code and refreshing blindly. You’ll use **lil-gui** (a modern replacement for dat.GUI), learn the common control types (range/checkbox/color/button), organize controls with folders, and avoid common pitfalls like “GUI can only tweak properties” and **GPU memory leaks** when regenerating geometry.

![Screen Shot 2025-12-27 at 13.12.08 PM.png](attachment:664b3047-c850-4da4-93bf-2f4a0d6429ad:Screen_Shot_2025-12-27_at_13.12.08_PM.png)


> 💡
>
> **Others**
>
> - [Dat.GUI](https://github.com/dataarts/dat.gui)
> - [lil-gui](https://lil-gui.georgealways.com/)
> - [control-panel](https://github.com/freeman-lab/control-panel)
> - [ControlKit](https://github.com/automat/controlkit.js)
> - [Uil](https://github.com/lo-th/uil)
> - [Tweakpane](https://tweakpane.github.io/docs/)
> - [Guify](https://github.com/colejd/guify)
> - [Oui](https://github.com/wearekuva/oui)
> - [Bruno Simon's debug panel](https://bruno-simon.com/#debug)
>
> **Three.js documentation**
>
> - [Color](https://threejs.org/docs/?q=color#api/en/math/Color)
> - [BoxGeometry](https://threejs.org/docs/?q=boxgeo#api/en/geometries/BoxGeometry)

---

## 1) Why debug UI matters (why you should care)

- Creative coding is all about **iteration**: the “right” color / speed / amount usually emerges by tweaking.
- Debug UI makes your project **collaborative**: non-devs can tune values without touching code.
- Best practice: add tweaks **as you build**, not at the end (otherwise you’ll skip it).

---

## 2) Why lil-gui (dat.GUI → lil-gui)

- dat.GUI stopped being actively maintained for a while and triggered NPM warnings historically.
- **lil-gui** became a popular **drop-in replacement** and has better features and a smaller footprint.
- If older videos say “dat.GUI” — treat it as “lil-gui” and move on.

---

## 3) Install + instantiate lil-gui

**Install**

- `npm install lil-gui`

**Import + create**

```js
import GUI from 'lil-gui'
const gui = new GUI()
```

(You may also see `import * as dat from 'lil-gui'` then `new dat.GUI()`—same idea.)

---

## 4) Control types you’ll use all the time

lil-gui auto-detects control type from the property value.

### A) Range (number)

- Add numeric properties with `gui.add(object, 'prop')`
- Add bounds + step:

```js
gui.add(mesh.position, 'y', -3, 3, 0.01)
// or chain style:
gui.add(mesh.position, 'y').min(-3).max(3).step(0.01).name('elevation')
```

### B) Checkbox (boolean)

- Example: hide/show object

```js
gui.add(mesh, 'visible')
gui.add(material, 'wireframe')
```

### C) Color (special case)

- Use `addColor()` instead of `add()`:

```js
gui.addColor(material, 'color')
```

**Gotcha:** Three.js color management can make the displayed value not match what you expect.

Two common solutions:

1. **Read internal color** on change (good for debugging, but requires console):
   - `value.getHexString()`
2. **Best practice**: store a plain `debugObject` color and apply to Three.js on change:

```js
const debugObject = { color: '#a778d8' }
const material = new THREE.MeshBasicMaterial({ color: debugObject.color })

gui.addColor(debugObject, 'color').onChange(() => {
  material.color.set(debugObject.color)
})
```

This keeps the source-of-truth in a plain object designers can edit without needing the console.

### D) Button (trigger a function)

GUI can only hook into object properties, so store functions on an object:

```js
debugObject.spin = () => {
  gsap.to(mesh.rotation, { duration: 1, y: mesh.rotation.y + Math.PI * 2 })
}

gui.add(debugObject, 'spin')
```

---

## 5) Big rule: GUI can tweak **properties**, not raw variables

This won’t work:

```js
let x = 1
gui.add(x, '???') // no
```

Do this instead:

```js
const debugObject = { x: 1 }
gui.add(debugObject, 'x')
```

Use this parameter-object pattern as your control hub.

---

## 6) Tweaking geometry parameters requires rebuilding the geometry

**Important idea:** constructor parameters like `widthSegments` are not live properties you can mutate after creation.

Plan:
1. store a `subdivision` value in `debugObject`
2. rebuild geometry when user finishes dragging
3. **dispose** the old geometry to avoid GPU memory leaks

```js
debugObject.subdivision = 2

gui.add(debugObject, 'subdivision')
  .min(1).max(20).step(1)
  .onFinishChange(() => {
    mesh.geometry.dispose()
    mesh.geometry = new THREE.BoxGeometry(
      1, 1, 1,
      debugObject.subdivision,
      debugObject.subdivision,
      debugObject.subdivision
    )
  })
```

**Why `onFinishChange`?**

- `onChange` fires constantly while dragging → heavy CPU / GPU work → lag.
- `onFinishChange` fires once when the user finishes interacting (better UX + performance).

Also, if you swap geometries make sure to dispose any unused buffers, materials, or textures to keep GPU memory stable.

---

## 7) Organize a crowded UI with folders

```js
const cubeTweaks = gui.addFolder('Awesome cube')

cubeTweaks.add(mesh.position, 'y').min(-3).max(3).step(0.01)
cubeTweaks.add(mesh, 'visible')
cubeTweaks.add(material, 'wireframe')
cubeTweaks.addColor(debugObject, 'color').onChange(() => material.color.set(debugObject.color))
cubeTweaks.add(debugObject, 'spin')
cubeTweaks.add(debugObject, 'subdivision').min(1).max(20).step(1)

// close folders by default
cubeTweaks.close()
```

You can nest folders if needed.

---

## 8) GUI configuration / UX tricks

You can control the panel itself:

```js
const gui = new GUI({
  width: 300,
  title: 'Nice debug UI',
  closeFolders: false
})
```

Visibility controls:

- `gui.close()` (collapse)
- `gui.hide()` (completely hide)

Toggle quickly with a keyboard shortcut:

```js
window.addEventListener('keydown', (event) => {
  if (event.key === 'h') gui.hide()
})
```

---

## Setup

Download [Node.js](https://nodejs.org/en/download/). Then run:

```bash
# Install dependencies (only the first time)
npm install

# Run the local server at localhost:8080
npm run dev

# Build for production in the dist/ directory
npm run build
```
