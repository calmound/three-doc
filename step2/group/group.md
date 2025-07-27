在三维场景中，`THREE.Group` 是一个用于包裹和管理其他对象的不可见容器。

它本身不具有几何形状或材质，其核心作用是将多个三维对象（如网格、光源、相机或其他分组）集合成一个单元，从而可以对整个集合进行统一的变换操作，如移动、旋转和缩放。

当需要构建由多个部分组成的复杂模型（例如一辆汽车、一个机器人或一个建筑）时，使用分组来组织场景结构是一种基础且高效的方法。

### 核心概念讲解

#### 定义与用途

`THREE.Group` 继承自 Three.js 的核心基类 `Object3D`，因此它具备了三维对象在场景中的所有基本属性，例如位置（`position`）、旋转（`rotation`）和缩放（`scale`）。

它的主要用途包括：

- **逻辑组织**: 将功能上或逻辑上相关的对象归为一组，使场景结构更加清晰、易于管理。
- **集体变换**: 对分组进行变换操作，该变换会应用于其包含的所有子对象。这极大地简化了对复杂组合体的控制。例如，移动一个包含车身和车轮的分组，会使整辆车一起移动。
- **坐标系管理**: 分组为其所有子对象建立了一个局部的坐标系。子对象的位置、旋转和缩放都是相对于父分组的。

大家通过下面的交互来体验下加组和不加组的区别

<iframe src="step2/group/show.html" width="100%" height="700"></iframe>

#### 构造与参数

`Group` 的构造函数非常直接，不需要任何参数。

```
const group = new THREE.Group();
```

#### 核心属性与方法

由于 `Group` 的主要功能是作为容器，其最核心的方法是用于管理其内容的 `add()` 和 `remove()`。同时，所有继承自 `Object3D` 的变换属性也至关重要。

- **`.add( object, ... )`**:
  - **功能**: 将一个或多个 `Object3D` 对象添加为该分组的子对象。
  - **使用场景**: 当创建了一个独立的物体（如 `Mesh`）后，使用此方法将其放入分组中进行管理。
- **`.remove( object, ... )`**:
  - **功能**: 从分组中移除一个或多个指定的子对象。被移除的对象将不再受该分组变换的影响。
  - **使用场景**: 需要将某个物体从组合体中分离出来时使用。
- **`.children`**:
  - **功能**: 一个数组，包含了该分组所有直接的子对象。
  - **使用场景**: 用于遍历或访问分组内的特定对象。一般不建议直接修改此数组，而应使用 `add()` 和 `remove()` 方法。
- **`.position`, `.rotation`, `.scale`**:
  - **功能**: 分别控制分组的平移、旋转和缩放。对这些属性的任何修改都会应用到所有子对象上。
  - **使用场景**: 这是实现对一组对象进行整体操控的关键。

#### 官方文档参考

要了解 `Group` 的所有属性和方法，可以查阅 Three.js 官方文档：

- **Group API 文档**: [https://threejs.org/docs/#api/zh/objects/Group](https://threejs.org/docs/#api/zh/objects/Group "null")

#### 注意事项

- **变换的叠加**: 子对象的最终世界坐标（全局坐标）是其自身局部变换与所有父级对象（包括分组）变换累积相乘的结果。
- **性能考量**: 虽然分组是组织场景的有效工具，但创建过深或过于复杂的层级结构可能会对性能产生轻微影响，因为每次更新都需要遍历场景图（Scene Graph）来计算最终变换。

### 动手实践：代码示例

以下代码创建了一个分组，其中包含一个中心立方体和一个环绕它的小立方体。通过旋转整个分组，可以观察到两个立方体作为一个整体进行旋转。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js Group 示例</title>
    <style>
      body {
        margin: 0;
        overflow: hidden;
      }
      canvas {
        display: block;
      }
    </style>
  </head>
  <body>
    <script type="importmap">
      {
        "imports": {
          "three": "https://unpkg.com/three@0.160.0/build/three.module.js"
        }
      }
    </script>
    <script type="module">
      import * as THREE from "three";

      // 1. 初始化场景、相机和渲染器
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0x111111); // 设置深色背景

      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.z = 10;

      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // 添加坐标轴辅助线，方便观察
      const axesHelper = new THREE.AxesHelper(5);
      scene.add(axesHelper);

      // 2. 创建一个分组
      const group = new THREE.Group();

      // 3. 创建两个不同的物体（Mesh）
      // 创建中心的大立方体
      const mainCubeGeometry = new THREE.BoxGeometry(2, 2, 2);
      const mainCubeMaterial = new THREE.MeshBasicMaterial({
        color: 0x00ff00,
        wireframe: true,
      });
      const mainCube = new THREE.Mesh(mainCubeGeometry, mainCubeMaterial);

      // 创建环绕的小立方体
      const smallCubeGeometry = new THREE.BoxGeometry(0.8, 0.8, 0.8);
      const smallCubeMaterial = new THREE.MeshBasicMaterial({
        color: 0xff00ff,
      });
      const smallCube = new THREE.Mesh(smallCubeGeometry, smallCubeMaterial);
      // 设置小立方体相对于分组原点的位置
      smallCube.position.x = 3;

      // 4. 将创建的物体添加到分组中
      group.add(mainCube);
      group.add(smallCube);

      // 5. 将整个分组添加到场景中
      scene.add(group);

      // 动画循环
      function animate() {
        requestAnimationFrame(animate);

        // 旋转整个分组
        // 可以看到两个立方体都围绕着分组的原点（世界坐标的0,0,0）进行旋转
        group.rotation.y += 0.01;
        group.rotation.x += 0.005;

        renderer.render(scene, camera);
      }

      // 监听窗口大小变化
      window.addEventListener("resize", () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      });

      animate();
    </script>
  </body>
</html>
```

</details>

<iframe src="step2/group/demo.html" width="100%" height="500"><iframe>
