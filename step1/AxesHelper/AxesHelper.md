在三维空间中，方向和位置是构建场景的基础。为了直观地表示场景中的坐标系，Three.js 提供了一个非常有用的辅助工具：`AxesHelper`。

`AxesHelper` 本质上是一个用于显示三维坐标轴的对象。它通过在场景中绘制三条分别代表 X、Y、Z 轴的彩色线条，为开发者提供了一个清晰的空间参考。这在调试相机位置、物体朝向或布局时尤其重要。要有效地利用这个工具，首先需要理解它的基本构成和使用方式。

### 核心概念讲解

#### 定义与用途

`AxesHelper` 是一个辅助对象，用于在场景原点 (0, 0, 0) 处可视化局部坐标系。它会渲染三条相互垂直的线段，分别代表三个坐标轴：

- **X 轴**: 红色 (Red)
- **Y 轴**: 绿色 (Green)
- **Z 轴**: 蓝色 (Blue)

它的主要用途是在开发和调试阶段，帮助开发者理解和确认物体在场景中的方位和旋转状态。

#### 构造与参数

`AxesHelper` 的构造方法非常简单，只有一个可选参数。

**构造方法:**

```
new THREE.AxesHelper(size)
```

**参数说明:**

| 参数   | 类型   | 描述                       | 默认值 |
| ------ | ------ | -------------------------- | ------ |
| `size` | Number | 可选。表示坐标轴线的长度。 | `1`    |

#### 核心方法

`AxesHelper` 继承自 `LineSegments`，因此它拥有 `Object3D` 的所有属性（如 `.position`, `.rotation`, `.scale`）。但其自身最常用的方法是用于自定义坐标轴颜色。

- **.setColors(xAxisColor, yAxisColor, zAxisColor):**
  - **功能:** 设置三条坐标轴的颜色。
  - **使用场景:** 当默认的红/绿/蓝配色与场景中的其他元素冲突，或需要根据特定逻辑高亮某个轴时使用。颜色参数可以是 `THREE.Color` 实例、十六进制字符串或十六进制数值。

#### 官方文档参考

要了解更详细的 API 信息，可以查阅 Three.js 官方文档： [https://threejs.org/docs/#api/zh/helpers/AxesHelper](https://threejs.org/docs/#api/zh/helpers/AxesHelper "null")

#### 注意事项

- `AxesHelper` 是一个 `Object3D` 对象，创建后必须使用 `scene.add()` 方法将其添加到场景中才能被渲染。
- 它本身不受场景中光照的影响，其颜色始终保持通过构造或 `.setColors()` 方法设定的值。
- 通常将 `AxesHelper` 放置在场景的根节点，或附加到特定对象上以观察该对象的局部坐标系。

### 代码示例

以下代码创建了一个基础的 Three.js 场景，并在其中添加了一个立方体和一个 `AxesHelper`。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Three.js AxesHelper 示例</title>
    <style>
        body { margin: 0; }
        canvas { display: block; }
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
        // 导入 Three.js 核心库
        import * as THREE from 'three';
        // 导入轨道控制器 (OrbitControls)
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

        // 1. 初始化场景
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x111111); // 设置场景背景色为深灰色

        // 2. 初始化相机
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(3, 4, 5); // 设置相机位置
        camera.lookAt(scene.position); // 相机朝向场景原点

        // 3. 初始化渲染器
        const renderer = new THREE.WebGLRenderer({ antialias: true }); // 开启抗锯齿
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // 4. 创建一个立方体作为参考物
        const geometry = new THREE.BoxGeometry(1, 1, 1);
        const material = new THREE.MeshNormalMaterial(); // 使用法向材质，方便观察面朝向
        const cube = new THREE.Mesh(geometry, material);
        cube.position.set(1, 0.5, 1); // 将立方体稍微移动，以便观察
        scene.add(cube);

        // 5. 创建并添加坐标轴辅助器
        // 创建一个长度为 3 的坐标轴辅助器
        const axesHelper = new THREE.AxesHelper(3);
        // 将其添加到场景中
        scene.add(axesHelper);

        // 6. 添加轨道控制器，允许用户通过鼠标交互
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true; // 启用阻尼效果，使交互更平滑

        // 7. 渲染循环
        function animate() {
            requestAnimationFrame(animate);

            controls.update(); // 更新控制器状态

            renderer.render(scene, camera);
        }

        // 8. 处理窗口大小变化
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // 启动渲染
        animate();
    </script>
</body>
</html>
```

</details>

<iframe src="step1/AxesHelper/demo.html" width="100%" height="500"></iframe>
