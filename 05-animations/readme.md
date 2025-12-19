# 05. Animations - Stop-Motion Rendering

Three.js animation works like a stop-motion flipbook: update values, render the frame, then repeat. Because devices run at different refresh rates, every animation should be frame-rate independent.

## Standard loop: requestAnimationFrame
- `requestAnimationFrame(fn)` runs `fn` on the next frame.
- Calling it again inside the same function creates the render loop:
```js
const tick = () => {
  // update values
  renderer.render(scene, camera)
  requestAnimationFrame(tick)
}
tick()
```

## Frame-rate independence with delta time
- Hard-coding steps like `mesh.rotation.y += 0.01` runs faster on high-refresh screens and slower on low-fps devices.
- Multiply motion by the time since the last frame:
```js
let previousTime = Date.now()

const tick = () => {
  const currentTime = Date.now()
  const deltaTime = currentTime - previousTime
  previousTime = currentTime

  mesh.rotation.y += 0.001 * deltaTime
  renderer.render(scene, camera)
  requestAnimationFrame(tick)
}
```

## Cleaner timing with THREE.Clock
- `THREE.Clock` handles timing for you.
- `getElapsedTime()` returns seconds since the clock started—good for looping math:
```js
const clock = new THREE.Clock()

const tick = () => {
  const t = clock.getElapsedTime()
  mesh.rotation.y = t
  renderer.render(scene, camera)
  requestAnimationFrame(tick)
}
```
- Handy patterns:
  - Circular motion: `mesh.position.x = Math.cos(t)` and `mesh.position.y = Math.sin(t)`
  - Camera orbits: move the camera with sin/cos and aim it using `camera.lookAt(mesh.position)`
- `clock.getDelta()` exists, but use it only when you understand its behavior.

## Authored motion with GSAP
- Use a tweening library for staged, timeline-like movement.
- Install and import:
  - `npm install gsap@3.12`
  - `import gsap from 'gsap'`
- Example:
```js
gsap.to(mesh.position, { duration: 1, delay: 1, x: 2 })
```
- Even with GSAP updating values, keep the render loop running so changes appear every frame.

## When to choose native vs GSAP
- Native + `Clock`: continuous or procedural motion (loops, orbiting, idle animations).
- GSAP: directed, authored motion (timing, easing, sequences, storytelling beats).

## Project setup
```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Production build to dist/
npm run build
```
