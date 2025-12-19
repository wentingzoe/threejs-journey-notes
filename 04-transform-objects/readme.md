# 04. Transform Objects - Position, Scale, Rotation, Groups

Quick notes from the “Transform Objects” lesson so you remember what changed and why.

## Key takeaways
- Positions: use `mesh.position.set(x, y, z)` and `vector.length()` helpers; `normalize()` is handy for direction-only vectors.
- Scale: `mesh.scale.set(x, y, z)` multiplies geometry dimensions without recreating it.
- Rotation: set `mesh.rotation` in radians; change order with `mesh.rotation.reorder('YXZ')` to avoid gimbal lock.
- Groups: create a `THREE.Group()` to move/scale/rotate multiple meshes together instead of updating each one.
- Look at: `camera.lookAt(target)` points the camera (or any object) at a position; pair with helpers like `AxesHelper` while debugging.

## Run it
```bash
# install deps once
npm install

# dev server
npm run dev

# production build
npm run build
```
