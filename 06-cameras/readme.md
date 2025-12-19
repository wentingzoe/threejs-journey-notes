# 06. Cameras - Perspective vs Orthographic

You’ll hear about many camera types, but you’ll mostly use two: `PerspectiveCamera` for realistic depth and `OrthographicCamera` for flat, UI-like views. Know the others exist (`ArrayCamera`, `StereoCamera`, `CubeCamera`) for split screens, VR-ish effects, or environment captures.

## Official docs
- [Camera](https://threejs.org/docs/#api/en/cameras/Camera)
- [PerspectiveCamera](https://threejs.org/docs/#api/en/cameras/PerspectiveCamera)
- [OrthographicCamera](https://threejs.org/docs/#api/en/cameras/OrthographicCamera)
- [ArrayCamera](https://threejs.org/docs/#api/en/cameras/ArrayCamera)
- [StereoCamera](https://threejs.org/docs/#api/en/cameras/StereoCamera)
- [CubeCamera](https://threejs.org/docs/#api/en/cameras/CubeCamera)
- [OrbitControls](https://threejs.org/docs/#examples/en/controls/OrbitControls)
- [FlyControls](https://threejs.org/docs/#examples/en/controls/FlyControls)
- [FirstPersonControls](https://threejs.org/docs/#examples/en/controls/FirstPersonControls)
- [PointerLockControls](https://threejs.org/docs/#examples/en/controls/PointerLockControls)
- [TrackballControls](https://threejs.org/docs/#examples/en/controls/TrackballControls)
- [TransformControls](https://threejs.org/docs/#examples/en/controls/TransformControls)
- [DragControls](https://threejs.org/docs/#examples/en/controls/DragControls)
- [Vector3](https://threejs.org/docs/#api/en/math/Vector3)
- [Euler](https://threejs.org/docs/#api/en/math/Euler)

## PerspectiveCamera essentials
```js
new THREE.PerspectiveCamera(fov, aspect, near, far)
```
- `fov` (degrees): vertical field of view, often 45–75.
- `aspect`: `width / height` (canvas ratio).
- `near` / `far`: clipping planes. Avoid tiny near + huge far or you risk z-fighting. Defaults like `0.1` and `100` are fine for most scenes.

## OrthographicCamera essentials
```js
new THREE.OrthographicCamera(left, right, top, bottom, near, far)
```
- No perspective—object size stays constant with distance.
- Adjust for aspect ratio to avoid stretching:
```js
const aspect = sizes.width / sizes.height
const camera = new THREE.OrthographicCamera(
  -1 * aspect, 1 * aspect,
  1, -1,
  0.1, 100
)
```

## Custom controls: cursor → camera
- Track the mouse and normalize to a centered range:
```js
const cursor = { x: 0, y: 0 }
window.addEventListener('mousemove', (event) => {
  cursor.x = event.clientX / sizes.width - 0.5
  cursor.y = -(event.clientY / sizes.height - 0.5) // invert Y
})
```
- Map cursor to camera motion inside `tick()` and keep aiming at the target:
```js
camera.position.x = cursor.x * 5
camera.position.y = cursor.y * 5
camera.lookAt(mesh.position)
```
- Orbit using circle math:
```js
camera.position.x = Math.sin(cursor.x * Math.PI * 2) * 2
camera.position.z = Math.cos(cursor.x * Math.PI * 2) * 2
camera.position.y = cursor.y * 3
camera.lookAt(mesh.position)
```

## Built-in controls: what they’re for
- OrbitControls: orbit + pan + zoom (most used).
- TrackballControls: like OrbitControls but can flip fully.
- FlyControls / FirstPersonControls / PointerLockControls: game-like movement styles.
- TransformControls / DragControls: move objects with gizmos/dragging (not camera motion).

## OrbitControls basics (the default choice)
- Import from examples (not on `THREE.*`):
```js
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'
const controls = new OrbitControls(camera, canvas)
```
- Set a target and update:
```js
controls.target.y = 2
controls.update()
```
- Enable damping for smooth, inertia-like motion (remember to update each frame):
```js
controls.enableDamping = true

// in tick()
controls.update()
```

## Project setup
```bash
npm install
npm run dev
npm run build
```
