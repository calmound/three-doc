在 Three.js 中，`GridHelper` 是一个用于创建平面网格的辅助对象。它在三维场景中渲染一个由线条构成的二维网格，通常位于 XZ 平面上。

作为开发过程中的一个可视化参考工具，`GridHelper` 对于确定场景中其他对象的位置、尺寸和相对关系至关重要。它提供了一个清晰的地面参照，使得布局和调试工作更加直观和高效。要有效利用此工具，需要先了解其构造方式和核心参数。

### 核心概念讲解

#### 定义与用途

`GridHelper` 的主要用途是在三维空间中创建一个参考网格。这个网格可以模拟一个地面或一个基准平面，帮助开发者在没有复杂模型的情况下，快速建立对场景空间感和尺度的认知。在搭建原型、对齐物体或调试相机位置时，它是一个不可或缺的辅助工具。

#### 构造与参数

`GridHelper` 通过其构造函数创建实例：

`new THREE.GridHelper(size, divisions, colorCenterLine, colorGrid)`

其参数定义如下：

| 参数              | 类型   | 含义                                                      | 默认值     |
| ----------------- | ------ | --------------------------------------------------------- | ---------- |
| `size`            | Number | 网格的总尺寸。网格将覆盖 `-size/2` 到 `size/2` 的范围。   | `10`       |
| `divisions`       | Number | 网格在每个轴向上划分的格数。                              | `10`       |
| `colorCenterLine` | Color  | 中心线的颜色。可以是 `Color` 实例、十六进制字符串或数字。 | `0x444444` |
| `colorGrid`       | Color  | 网格线的颜色。可以是 `Color` 实例、十六进制字符串或数字。 | `0x888888` |

#### 核心属性与方法

由于 `GridHelper` 主要用于显示，其大部分操作在构造时完成。以下是几个常用的相关属性：

1. **.material.color**: 虽然构造时可以设置颜色，但通过修改材质的颜色属性，可以在运行时动态改变整个网格的颜色（中心线和网格线会变为同一颜色）。
2. **.position**: `GridHelper` 继承自 `Object3D`，因此拥有 `position` 属性。可以修改其 `y` 值来调整网格在垂直方向上的位置，以匹配不同的场景需求。
3. **.rotation**: 同样继承自 `Object3D`，可以通过 `rotation` 属性旋转网格。例如，`grid.rotation.x = Math.PI / 2` 可以将网格从 XZ 平面旋转至 XY 平面。

#### 官方文档参考

要获取更全面的 API 信息，请查阅 Three.js 官方文档： [https://threejs.org/docs/#api/zh/helpers/GridHelper](https://threejs.org/docs/#api/zh/helpers/GridHelper "null")

#### 注意事项

- `GridHelper` 本质上是一个 `LineSegments` 对象。
- 默认情况下，网格创建于 XZ 平面，其中心点位于场景原点 `(0, 0, 0)`。
- 网格的精细度由 `divisions` 参数决定，过高的值可能会对性能产生轻微影响，但通常可忽略不计。

### 代码示例

以下代码创建了一个基础的 Three.js 场景，并在其中添加了一个 `GridHelper` 和一个立方体，以展示其作为参考平面的作用。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js GridHelper 示例</title>
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
          "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
          "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
        }
      }
    </script>
    <script type="module">
      // 引入 Three.js 核心库和轨道控制器
      import * as THREE from "three";
      import { OrbitControls } from "three/addons/controls/OrbitControls.js";

      // 1. 初始化场景
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0x111111); // 设置场景背景色

      // 2. 初始化相机
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.set(10, 15, 10); // 设置相机位置
      camera.lookAt(scene.position); // 相机望向场景中心

      // 3. 初始化渲染器
      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // 4. 添加 GridHelper
      // 网格总尺寸为 50x50，沿每个轴划分 20 个格子
      const size = 50;
      const divisions = 20;
      const gridHelper = new THREE.GridHelper(size, divisions);
      scene.add(gridHelper); // 将网格添加到场景中

      // 5. 添加一个立方体作为参照物
      const geometry = new THREE.BoxGeometry(4, 4, 4);
      const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
      const cube = new THREE.Mesh(geometry, material);
      cube.position.y = 2; // 将立方体向上移动，使其底部与网格对齐
      scene.add(cube);

      // 6. 添加光源
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      scene.add(ambientLight);
      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(5, 10, 7.5);
      scene.add(directionalLight);

      // 7. 添加轨道控制器
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.enableDamping = true; // 启用阻尼效果，使旋转更平滑

      // 8. 渲染循环
      function animate() {
        requestAnimationFrame(animate);
        controls.update(); // 更新控制器
        renderer.render(scene, camera);
      }

      // 9. 监听窗口尺寸变化
      window.addEventListener("resize", () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      });

      animate(); // 启动渲染循环
    </script>
  </body>
</html>
```

</details>

<iframe src="step1/gridhelper/demo.html" width="100%" height="500"></iframe>

#### 实现逻辑说明

1. **场景初始化**: 创建 `Scene`、`PerspectiveCamera` 和 `WebGLRenderer`，构成 Three.js 渲染所需的基本环境。
2. **GridHelper 创建**: 调用 `new THREE.GridHelper(50, 20)` 创建一个 50x50 大小、20x20 格的网格。由于未指定颜色，它将使用默认的灰色。
3. **添加至场景**: 通过 `scene.add(gridHelper)` 将创建的网格添加到场景中，使其能够被渲染。
4. **添加参照物**: 创建一个立方体 `Mesh` 并将其放置在网格上方，这样可以直观地看到网格作为地面的参照效果。
5. **控制器与渲染**: 添加 `OrbitControls` 以便通过鼠标交互来旋转和缩放场景，最后在 `animate` 函数中持续渲染场景。
