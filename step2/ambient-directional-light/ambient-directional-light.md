在三维世界中，光是决定场景视觉呈现的关键因素。没有光，物体将不可见。Three.js 中的光源模拟了现实世界的光照效果，为场景赋予了深度、色彩和真实感。正确地使用光源，是构建可信、生动三维场景的基础。

环境光 (`AmbientLight`) 和平行光 (`DirectionalLight`) 是最基础也是最常用的两种光源类型，它们共同构成了场景光照的基本框架。

大家先通过下面的交互有一个直观的感受。

<iframe src="step2/ambient-directional-light/show.html" width="100%" height="700"></iframe>

### 核心概念讲解：环境光

#### 定义与用途

环境光（`AmbientLight`）是一种无特定方向的光源，它会均匀地照亮场景中的所有物体。可以将其理解为充满了整个空间的散射光，它没有明确的来源，因此不会在物体上形成阴影或高光。

其主要用途是为整个场景提供一个基础亮度，防止物体背光面或未被其他光源照射到的区域陷入完全的黑暗，从而提升场景的整体真实感。它通常与其他有方向性的光源结合使用。

#### 构造与参数

通过 `THREE.AmbientLight` 类来创建环境光。

**构造方法:** `new THREE.AmbientLight(color, intensity)`

**参数说明:**

| 参数        | 类型      | 描述                           | 默认值             |
| ----------- | --------- | ------------------------------ | ------------------ |
| `color`     | `Integer` | （可选）光源的十六进制颜色值。 | `0xffffff`（白色） |
| `intensity` | `Float`   | （可选）光源的强度或亮度。     | `1.0`              |

#### 核心属性

- **`.color`**: 获取或设置光源的颜色。
- **`.intensity`**: 获取或设置光源的强度。数值越大，基础亮度越高。

#### 注意事项

- 环境光自身无法产生阴影，因为它不具备方向性。
- 单独使用环境光会使场景看起来平淡、缺乏立体感，因为它无法突出物体的轮廓和表面细节。

#### 官方文档参考

- **AmbientLight**: [https://threejs.org/docs/index.html#api/zh/lights/AmbientLight](https://threejs.org/docs/index.html#api/zh/lights/AmbientLight)

### 核心概念讲解：平行光

#### 定义与用途

平行光（`DirectionalLight`）是一种有特定方向的光源，它的光线平行且均匀地照射向场景中的物体。可以将其视为来自于无限远处的光源，例如太阳光。

平行光的主要特点是：

- 光线具有方向性，能够在物体上形成清晰的阴影。
- 光线强度沿着光源的照射方向衰减，离光源越远，光线强度越弱。

平行光适用于模拟自然界中的光源，特别是太阳光。它能够为场景提供明确的光照方向和阴影效果，从而增强场景的立体感和真实感。

#### 构造与参数

通过 `THREE.DirectionalLight` 类来创建平行光。

**构造方法:** `new THREE.DirectionalLight(color, intensity)`

**参数说明:**

| 参数        | 类型      | 描述                           | 默认值             |
| ----------- | --------- | ------------------------------ | ------------------ |
| `color`     | `Integer` | （可选）光源的十六进制颜色值。 | `0xffffff`（白色） |
| `intensity` | `Float`   | （可选）光源的强度或亮度。     | `1.0`              |

#### 核心属性

- **`.color`**: 获取或设置光源的颜色。
- **`.intensity`**: 获取或设置光源的强度。数值越大，光线越亮。
- **`.position`**: 获取或设置光源在三维空间中的位置。
- **`.target`**: 获取或设置光源照射的目标对象。

#### 注意事项

- 平行光的阴影效果依赖于物体与光源之间的相对位置关系。
- 为了获得更自然的光照效果，平行光通常与环境光等其他光源结合使用。

#### 官方文档参考

- **DirectionalLight**: [https://threejs.org/docs/index.html#api/zh/lights/DirectionalLight](https://threejs.org/docs/index.html#api/zh/lights/DirectionalLight)

<details>
<summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Three.js Raycaster 示例</title>
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

        // 1. 初始化场景、相机和渲染器
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0xf0f0f0);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 10;

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        const controls = new OrbitControls(camera, renderer.domElement);

        // 2. 创建物体
        const objects = []; // 用于存储所有可被拾取的物体
        const geometry = new THREE.BoxGeometry(1, 1, 1);

        for (let i = 0; i < 3; i++) {
            const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
            const cube = new THREE.Mesh(geometry, material);
            cube.position.x = (i - 1) * 3; // 将立方体分开排列
            scene.add(cube);
            objects.push(cube); // 将立方体添加到检测列表
        }

        // 3. 初始化 Raycaster 和鼠标向量
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();
        let intersectedObject = null; // 用于存储当前被选中的物体

        // 4. 监听鼠标点击事件
        window.addEventListener('click', onMouseClick);

        function onMouseClick(event) {
            // 将鼠标位置归一化为设备坐标。x 和 y 方向的取值范围是 (-1 to +1)
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = - (event.clientY / window.innerHeight) * 2 + 1;

            // 通过摄像机和鼠标位置更新射线
            raycaster.setFromCamera(mouse, camera);

            // 计算物体和射线的交点
            const intersects = raycaster.intersectObjects(objects);

            // 恢复上一个被选中物体的颜色
            if (intersectedObject) {
                intersectedObject.material.color.set(0x00ff00);
            }

            if (intersects.length > 0) {
                // intersects[0] 是最近的交点
                intersectedObject = intersects[0].object;
                // 将选中物体的颜色变为红色
                intersectedObject.material.color.set(0xff0000);
            } else {
                intersectedObject = null;
            }
        }

        // 窗口自适应
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // 渲染循环
        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            renderer.render(scene, camera);
        }

        animate();
    </script>
</body>
</html>
```

</details>

<iframe src="step2/ambient-directional-light/demo.html" width="100%" height="700"></iframe>
