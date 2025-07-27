# Built-in Geometry

在 Three.js 中，**内置几何体（Built-in Geometries）** 是预先定义好的一系列三维形状的类，例如球体、圆柱体、圆环体等。它们提供了创建复杂三维场景所需的基础构建模块。

通过实例化这些几何体类，开发者可以快速生成顶点数据和面信息，而无需手动计算每个点的位置。这极大地简化了三维对象的创建流程，是构建任何 Three.js 场景的起点。当这些几何体与材质（Materials）结合后，便构成了可以在场景中渲染的网格（Meshes）。

官方网址案例，将每个几何体都进行了展示，很直观，大家可以先体验一下：https://threejs.org/manual/#zh/primitives

### **核心概念讲解**

Three.js 提供了丰富的几何体选项，每种都有其特定的构造参数，以控制最终的形状和细节。

#### **球体几何体 (`SphereGeometry`)**

- **定义与用途:** 用于创建理想的球形。常用于模拟行星、球、粒子等圆形物体。
- **构造与参数:**
  ```
  new THREE.SphereGeometry(radius, widthSegments, heightSegments, phiStart, phiLength, thetaStart, thetaLength)
  ```
  **关键参数说明:** | 参数 | 类型 | 含义 | 默认值 | | :--- | :--- | :--- | :--- | | `radius` | Float | 球体半径 | 1 | | `widthSegments` | Integer | 水平分段数，数值越高，球体越平滑 | 32 | | `heightSegments` | Integer | 垂直分段数，数值越高，球体越平滑 | 16 |
- **核心属性:**
  - `.parameters`: 一个包含构造函数所有参数的对象。这对于在运行时查询或克隆几何体非常有用。
- **注意事项:**
  - 分段数（`widthSegments` 和 `heightSegments`）直接影响渲染性能。过高的值会产生大量三角形，增加 GPU 负载。
- **官方文档参考:**
  - [THREE.SphereGeometry](https://threejs.org/docs/#api/zh/geometries/SphereGeometry)

#### **圆柱几何体 (`CylinderGeometry`)**

- **定义与用途:** 用于创建圆柱形。可用于模拟柱子、管道、棒状物等。
- **构造与参数:**
  ```
  new THREE.CylinderGeometry(radiusTop, radiusBottom, height, radialSegments, heightSegments, openEnded, thetaStart, thetaLength)
  ```
  **关键参数说明:** | 参数 | 类型 | 含义 | 默认值 | | :--- | :--- | :--- | :--- | | `radiusTop` | Float | 顶部半径 | 1 | | `radiusBottom` | Float | 底部半径 | 1 | | `height` | Float | 圆柱高度 | 1 | | `radialSegments` | Integer | 径向分段数，决定了圆柱的“圆”度 | 32 |
- **注意事项:**
  - 通过设置不同的 `radiusTop` 和 `radiusBottom`，可以创建出圆台。
  - `openEnded` 参数可以用来控制圆柱是否封闭底部，适用于需要开放端口的管道模型。
  - 圆柱的高度（`height`）与半径（`radiusTop` 和 `radiusBottom`）的比例会影响圆柱的视觉稳定性，过高的圆柱可能看起来不够稳定。
  - **性能提示:** 圆柱的径向分段数（`radialSegments`）对性能影响较大，建议根据实际需求调整，避免不必要的性能消耗。
- **官方文档参考:**
  - [THREE.CylinderGeometry](https://threejs.org/docs/#api/zh/geometries/CylinderGeometry)

#### **圆环几何体 (`RingGeometry`)**

- **定义与用途:** 用于创建圆环形状。常用于模拟光环、戒指、轨道等。
- **构造与参数:**
  ```
  new THREE.RingGeometry(innerRadius, outerRadius, thetaSegments, phiSegments, thetaStart, thetaLength)
  ```
  **关键参数说明:** | 参数 | 类型 | 含义 | 默认值 | | :--- | :--- | :--- | :--- | | `innerRadius` | Float | 内圈半径 | 0.5 | | `outerRadius` | Float | 外圈半径 | 1 | | `thetaSegments` | Integer | 径向分段数，决定圆环的“圆”度 | 32 |
- **注意事项:**
  - 内外圈半径（`innerRadius` 和 `outerRadius`）决定了圆环的厚度，二者之差即为圆环的宽度。
  - 圆环的径向分段数（`thetaSegments`）对圆环的平滑度和性能有直接影响，建议根据实际需求调整。
  - **应用实例:** 圆环几何体非常适合用于创建光环效果，只需将材质设置为半透明并发光即可。
- **官方文档参考:**
  - [THREE.RingGeometry](https://threejs.org/docs/#api/zh/geometries/RingGeometry)

#### **平面几何体 (`PlaneGeometry`)**

- **定义与用途:** 用于创建平面形状。广泛用于地面、墙壁、幕布等。
- **构造与参数:**
  ```
  new THREE.PlaneGeometry(width, height, widthSegments, heightSegments)
  ```
  **关键参数说明:** | 参数 | 类型 | 含义 | 默认值 | | :--- | :--- | :--- | :--- | | `width` | Float | 平面宽度 | 1 | | `height` | Float | 平面高度 | 1 | | `widthSegments` | Integer | 水平分段数 | 1 | | `heightSegments` | Integer | 垂直分段数 | 1 |
- **注意事项:**
  - 平面的宽度和高度决定了其在三维空间中的大小，单位为与场景相同的单位（通常是米）。
  - 分段数（`widthSegments` 和 `heightSegments`）决定了平面的细分程度，数值越高，平面越平滑，但也会增加渲染负担。
  - **性能提示:** 对于大面积的平面，建议适当降低分段数，以获得更好的渲染性能。
- **官方文档参考:**
  - [THREE.PlaneGeometry](https://threejs.org/docs/#api/zh/geometries/PlaneGeometry)

#### **立方体几何体 (`BoxGeometry`)**

- **定义与用途:** 用于创建立方体或长方体形状。适用于建筑、家具、盒子等物体。
- **构造与参数:**
  ```
  new THREE.BoxGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
  ```
  **关键参数说明:** | 参数 | 类型 | 含义 | 默认值 | | :--- | :--- | :--- | :--- | | `width` | Float | 立方体宽度 | 1 | | `height` | Float | 立方体高度 | 1 | | `depth` | Float | 立方体深度 | 1 | | `widthSegments` | Integer | 宽度方向分段数 | 1 | | `heightSegments` | Integer | 高度方向分段数 | 1 | | `depthSegments` | Integer | 深度方向分段数 | 1 |
- **注意事项:**
  - 立方体的宽度、高度和深度决定了其在三维空间中的大小，单位为与场景相同的单位（通常是米）。
  - 分段数（`widthSegments`、`heightSegments` 和 `depthSegments`）决定了立方体每个面的细分程度，数值越高，立方体越平滑，但也会增加渲染负担。
  - **性能提示:** 对于复杂场景中的立方体，建议适当降低分段数，以获得更好的渲染性能。
- **官方文档参考:**
  - [THREE.BoxGeometry](https://threejs.org/docs/#api/zh/geometries/BoxGeometry)

#### **其他几何体**

除了上述几何体，Three.js 还内置了其他多种几何体，如 **圆锥几何体（`ConeGeometry`）**、**八面体几何体（`OctahedronGeometry`）**、**胶囊体（`CapsuleGeometry`）** 等，开发者可以根据需求选择合适的几何体进行使用。

每种几何体的具体构造参数和使用方法，请参考官方文档中的几何体部分。
