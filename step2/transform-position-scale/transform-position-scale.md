在三维空间中，物体的变换 (Transformation) 是指改变物体的位置、旋转和缩放。这是构建动态、交互式三维场景的基础。在 Three.js 中，所有可被放置于场景中的对象（如网格、灯光、相机）都继承自 `Object3D` 类，该类提供了执行这些变换所需的核心属性。对这些属性的精确控制，是实现预期视觉效果的关键。

### 核心概念讲解

#### 位置 (Position)

**1. 定义与用途**

`Object3D.position` 属性用于定义一个物体在三维空间中的局部坐标。它决定了物体相对于其父对象的位置。如果一个物体没有父对象（即直接被添加到场景 `Scene` 中），那么它的位置就是相对于世界坐标系的原点 (0, 0, 0)。

通过下面的交互大家先感受下

<iframe src="step2/transform-position-scale/show.html" width="100%" height="700"></iframe>

**2. 核心对象**

`position` 属性是 `Vector3`（三维向量）类的一个实例。它并非通过构造函数直接创建，而是在创建 `Object3D` 实例时自动生成。

`Vector3` 包含了三个分量，分别代表在 X、Y、Z 轴上的坐标。

| 属性 | 类型   | 含义         | 默认值 |
| ---- | ------ | ------------ | ------ |
| `x`  | Number | X 轴上的坐标 | `0`    |
| `y`  | Number | Y 轴上的坐标 | `0`    |
| `z`  | Number | Z 轴上的坐标 | `0`    |

**3. 核心属性与方法**

- **属性 `x`, `y`, `z`**: 直接读写向量在各个轴上的分量。
  - _使用场景_: 需要单独修改某一轴向的坐标值。
  - _示例_: `mesh.position.x = 5;`
- **方法 `.set(x, y, z)`**: 一次性设置向量的 x, y, z 三个分量。
  - _使用场景_: 需要同时初始化或重置物体的位置。
  - _示例_: `mesh.position.set(5, 2, -3);`
- **方法 `.copy(v)`**: 将另一个 `Vector3` 对象 `v` 的值复制到当前向量。
  - _使用场景_: 使一个物体的位置与另一个物体保持一致。
  - _示例_: `mesh2.position.copy(mesh1.position);`

**4. 官方文档参考**

- [Object3D.position](https://threejs.org/docs/#api/zh/core/Object3D.position)
- [Vector3](https://threejs.org/docs/#api/zh/math/Vector3)

### 代码示例

以下代码创建了两个立方体。一个位于场景中心，作为参照物。另一个则通过修改 position 和 scale 属性，展示了变换的效果。

<details>
<summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Three.js 位置与缩放示例</title>
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
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

        // 1. 初始化场景
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0xf0f0f0);

        // 2. 初始化相机
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(5, 5, 10); // 将相机向后移动，以便观察场景

        // 3. 初始化渲染器
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // 4. 添加坐标轴辅助器
        const axesHelper = new THREE.AxesHelper(5); // 辅助线长度为5
        scene.add(axesHelper);

        // 5. 创建几何体和材质
        const geometry = new THREE.BoxGeometry(1, 1, 1); // 创建一个 1x1x1 的立方体几何体

        // 创建参照物（红色立方体）
        const materialRef = new THREE.MeshBasicMaterial({ color: 0xff0000, wireframe: true });
        const referenceCube = new THREE.Mesh(geometry, materialRef);
        // 参照物位于世界坐标原点 (0,0,0)，缩放为 (1,1,1)
        scene.add(referenceCube);

        // 创建变换对象（蓝色立方体）
        const materialTransformed = new THREE.MeshBasicMaterial({ color: 0x0000ff });
        const transformedCube = new THREE.Mesh(geometry, materialTransformed);

        // --- 核心操作：修改位置和缩放 ---

        // (A) 设置位置
        // 将物体沿 X 轴正方向移动 3 个单位，沿 Y 轴正方向移动 2 个单位
        transformedCube.position.set(3, 2, 0);

        // (B) 设置缩放
        // 将物体在 X 轴方向放大 1.5 倍，Y 轴方向缩小至 0.5 倍，Z 轴方向不变
        transformedCube.scale.set(1.5, 0.5, 1);

        // 将变换后的立方体添加到场景中
        scene.add(transformedCube);

        // 6. 添加轨道控制器
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;

        // 7. 渲染循环
        function animate() {
            requestAnimationFrame(animate);
            controls.update(); // 更新控制器
            renderer.render(scene, camera);
        }

        animate();

        // 8. 窗口自适应
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

    </script>
</body>
</html>

```

</details>

<iframe src="step2/transform-position-scale/demo.html" width="100%" height="500"></iframe>
