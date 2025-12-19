# 07. Fullscreen and Resizing - Viewport-Safe Canvas

## Official ref
- [dblclick (MDN)](https://developer.mozilla.org/docs/Web/API/Element/dblclick_event)

## 1) Fill the viewport
Replace fixed sizes with the real viewport:
```js
const sizes = {
  width: window.innerWidth,
  height: window.innerHeight,
}
```

## 2) Reset margins and pin the canvas
Remove default whitespace and lock the canvas to the top-left:
```css
* { margin: 0; padding: 0; }

.webgl {
  position: fixed;
  top: 0;
  left: 0;
  outline: none; /* remove blue focus ring */
}

html, body { overflow: hidden; }
```

## 3) Handle window resize (sizes, camera, renderer)
Update all three on `resize`:
```js
window.addEventListener('resize', () => {
  // sizes
  sizes.width = window.innerWidth
  sizes.height = window.innerHeight

  // camera
  camera.aspect = sizes.width / sizes.height
  camera.updateProjectionMatrix()

  // renderer
  renderer.setSize(sizes.width, sizes.height)
})
```

## 4) Cap pixel ratio for sharpness vs perf
High DPI costs performance; cap at 2:
```js
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
```
Do this during setup and inside the resize handler:
```js
window.addEventListener('resize', () => {
  // ...
  renderer.setSize(sizes.width, sizes.height)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
})
```

## 5) Toggle fullscreen on double-click
Basic version:
```js
window.addEventListener('dblclick', () => {
  if (!document.fullscreenElement) {
    canvas.requestFullscreen()
  } else {
    document.exitFullscreen()
  }
})
```
Cross-browser (WebKit) fallback:
```js
window.addEventListener('dblclick', () => {
  const fullscreenElement = document.fullscreenElement || document.webkitFullscreenElement

  if (!fullscreenElement) {
    if (canvas.requestFullscreen) canvas.requestFullscreen()
    else if (canvas.webkitRequestFullscreen) canvas.webkitRequestFullscreen()
  } else {
    if (document.exitFullscreen) document.exitFullscreen()
    else if (document.webkitExitFullscreen) document.webkitExitFullscreen()
  }
})
```

## Project setup
```bash
npm install
npm run dev
npm run build
```
