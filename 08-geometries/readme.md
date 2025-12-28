# 0.8 Geometries
## Summary

- Geometries hold a mesh’s shape data (vertices, faces, plus extras like UVs and normals) and every built-in Three.js shape ultimately extends `BufferGeometry`.
- Segment counts drive visual smoothness versus performance; wireframes help see triangle density.
- Custom shapes come from `BufferGeometry` + typed arrays, and indexing lets you reuse vertices efficiently.

---

## 1) What a geometry really is

- A **geometry** is the shape data:
  - **vertices** = points in 3D space
  - **faces** = triangles that connect vertices to form surfaces
- Geometries aren’t only for meshes—later you can use vertices as **particles**.
- Vertices can store more than position (e.g., **UVs** for textures, **normals** for lighting).



## 2) Built-in geometries = mostly “ready-made shapes”

- Three.js provides many built-in geometries (Box, Plane, Sphere, Torus, Cylinder, Text, Tube, Extrude…).
- They all inherit from **`BufferGeometry`**, which has helpful transform methods like `translate()`, `rotateX()`, `normalize()`, etc.
- You don’t need to memorize every constructor—just know they exist and check docs when needed.



## 3) Geometry parameters matter (especially segments)

Example: `BoxGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)`

- **Segments** control subdivision (how many triangles make up each face).
- More segments → more detail potential (useful for curved shapes) but **more vertices = worse performance**.

To visualize triangles, use **wireframe**:

```js
const material = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true })
```

Example where segments matter a lot:

- `SphereGeometry(1, 32, 32)` looks smooth because it has enough subdivisions.



## 4) Making your own shape: `BufferGeometry`

When you need a custom simple geometry, build it directly with a **typed array**:

- Use `Float32Array` to store vertex positions in a flat list:
  - `(x, y, z)` for vertex 1, then `(x, y, z)` for vertex 2, etc.
- Wrap it in a `BufferAttribute`, then attach it to the geometry under the name **`position`** (important: shaders expect it).

Conceptually:

- `positionsArray` → `BufferAttribute(…, 3)` → `geometry.setAttribute('position', …)`

Triangle example:

- 3 vertices × 3 values each = **9 floats**.

Random triangles example:

- `count` triangles
- each triangle = 3 vertices
- each vertex = 3 floats
- total floats = `count * 3 * 3`



## 5) Optimization note: indexing

- `BufferGeometry` can reuse vertices via an **index** (corner vertices shared across faces).
- This reduces data and can improve performance (covered later).

---

> 💡 **Three.js documentation**
>
> - [BoxGeometry](https://threejs.org/docs/#api/en/geometries/BoxGeometry)
> - [BufferGeometry](https://threejs.org/docs/#api/en/core/BufferGeometry)
> - [PlaneGeometry](https://threejs.org/docs/#api/en/geometries/PlaneGeometry)
> - [CircleGeometry](https://threejs.org/docs/#api/en/geometries/CircleGeometry)
> - [ConeGeometry](https://threejs.org/docs/#api/en/geometries/ConeGeometry)
> - [CylinderGeometry](https://threejs.org/docs/#api/en/geometries/CylinderGeometry)
> - [RingGeometry](https://threejs.org/docs/#api/en/geometries/RingGeometry)
> - [TorusGeometry](https://threejs.org/docs/#api/en/geometries/TorusGeometry)
> - [TorusKnotGeometry](https://threejs.org/docs/#api/en/geometries/TorusKnotGeometry)
> - [DodecahedronGeometry](https://threejs.org/docs/#api/en/geometries/DodecahedronGeometry)
> - [OctahedronGeometry](https://threejs.org/docs/#api/en/geometries/OctahedronGeometry)
> - [TetrahedronGeometry](https://threejs.org/docs/#api/en/geometries/TetrahedronGeometry)
> - [IcosahedronGeometry](https://threejs.org/docs/#api/en/geometries/IcosahedronGeometry)
> - [SphereGeometry](https://threejs.org/docs/#api/en/geometries/SphereGeometry)
> - [ShapeGeometry](https://threejs.org/docs/#api/en/geometries/ShapeGeometry)
> - [TubeGeometry](https://threejs.org/docs/#api/en/geometries/TubeGeometry)
> - [ExtrudeGeometry](https://threejs.org/docs/#api/en/geometries/ExtrudeGeometry)
> - [LatheGeometry](https://threejs.org/docs/#api/en/geometries/LatheGeometry)
> - [TextGeometry](https://threejs.org/docs/?q=textge#examples/en/geometries/TextGeometry)
> - [Face3](https://threejs.org/docs/#api/en/core/Face3)
>
> **Others**
>
> - [Float32Array (MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Float32Array)

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
