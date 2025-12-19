# 03. First Project - Minimal Three.js Setup

Quick reminder of how to spin up the exercise and what matters in the initial Three.js setup.

## Minimal setup steps (from scratch)
- Prereqs: install Node.js (LTS) and check with `node -v`.
- Init project: `npm init -y` to create `package.json`.
- Add Vite: `npm install vite`.
- Add scripts in `package.json`:
```json
"scripts": {
  "dev": "vite",
  "build": "vite build"
}
```
- Run: `npm run dev` and open the localhost URL Vite prints (terminal stays “stuck” while the server runs).

## Vite basics to remember
- Use `<script type="module">` because you’re relying on ES modules.
- Vite provides auto-reload/HMR on save.
- Share `package.json` (and `package-lock.json`), not `node_modules/`; others run `npm install`.

## Three.js setup
- Install/import Three.js:
```js
import * as THREE from 'three'
```
- Minimum pieces to render:
  1) Scene:
  ```js
  const scene = new THREE.Scene()
  ```
  2) Object (mesh = geometry + material):
  ```js
  const geometry = new THREE.BoxGeometry(1, 1, 1)
  const material = new THREE.MeshBasicMaterial({ color: 0xff0000 })
  const mesh = new THREE.Mesh(geometry, material)
  scene.add(mesh)
  ```
  - Colors can be hex numbers (`0xff0000`), hex strings (`'#ff0000'`), names (`'red'`), or `THREE.Color`.
  3) Camera (Perspective):
  ```js
  const sizes = { width: 800, height: 600 }
  const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height)
  camera.position.z = 3
  scene.add(camera)
  ```
  - If you don’t move the camera off the origin, you’ll see black because you’re inside the cube.
  4) Renderer (draws to `<canvas>`):
  ```html
  <canvas class="webgl"></canvas>
  ```
  ```js
  const canvas = document.querySelector('canvas.webgl')
  const renderer = new THREE.WebGLRenderer({ canvas })
  renderer.setSize(sizes.width, sizes.height)
  ```
- First render:
```js
renderer.render(scene, camera)
```

## Why the screen was black
- Default positions put camera and cube at `(0, 0, 0)`, so you’re inside the cube. Move the camera (e.g., `camera.position.z = 3`) to see it.
