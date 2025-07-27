在三维场景中，除了提供均匀光照的环境光和平行光，还需要能够模拟局部照明的光源。

点光源 (`PointLight`) 和聚光灯 (`SpotLight`) 正是为此而设计的，它们能够从空间中的特定点向外发光，是构建逼真光影效果的关键工具。这两种光源都遵循光照随距离衰减的物理规律，并能投射阴影，极大地增强了场景的深度感和真实感。

大家先通过下面的交互有一个直观的感受。

<iframe src="step2/point-spot-light/show.html" width="100%" height="700"></iframe>

### 点光源 (PointLight)

#### 定义与用途

点光源（`PointLight`）模拟从空间中一个点向所有方向均匀发射的光源，类似于一个不带灯罩的灯泡。光线强度会随着与光源距离的增加而衰减。它常用于模拟灯泡、萤火虫或任何需要从单点向周围发光的场景。

#### 构造与参数

`PointLight` 的构造函数如下：

```
new THREE.PointLight(color, intensity, distance, decay)
```

其核心参数说明如下：

| 参数        | 类型   | 含义                                                                 | 默认值             |
| ----------- | ------ | -------------------------------------------------------------------- | ------------------ |
| `color`     | Color  | 光源颜色。                                                           | `0xffffff`（白色） |
| `intensity` | Number | 光照强度（流明）。                                                   | `1`                |
| `distance`  | Number | 光线照射的最大距离。当值为 `0` 时，光线可无限远。                    | `0`                |
| `decay`     | Number | 光照强度随距离衰减的速率。在物理正确的渲染模式下，`decay` 应为 `2`。 | `2`                |

#### 核心属性与方法

- **`.position`**: 类型为 `Vector3`。这是 `Light` 基类继承来的属性，也是点光源最重要的属性，用于设定光源在三维空间中的位置。
- **`.distance`**: 控制光线的有效范围。如果一个物体与光源的距离超过此值，将不会被照亮。
- **`.decay`**: 定义了光强如何随距离衰减。非物理正确的 `decay = 1` 会产生线性衰减，而物理正确的 `decay = 2` 会产生更真实的平方反比衰减。

#### 注意事项

- 点光源向所有方向发射光线，因此其阴影计算的开销相对较大，因为它需要渲染场景六次（每个方向一次）来生成阴影贴图。
- `distance` 和 `decay` 属性共同决定了光照的衰减效果，调整它们可以实现从柔和衰减到快速截止的各种光照边界。

#### 官方文档参考

- [Three.js PointLight Documentation](https://threejs.org/docs/#api/zh/lights/PointLight)

### 聚光灯 (SpotLight)

#### 定义与用途

聚光灯（`SpotLight`）模拟从一个点向特定方向发射的光束，类似于手电筒或舞台聚光灯。与点光源不同，聚光灯的光线是有方向性的，并且可以通过调整其角度和衰减参数来精确控制光束的范围和强度。聚光灯常用于强调场景中的特定对象或区域，创造戏剧性的光影效果。

#### 构造与参数

`SpotLight` 的构造函数如下：

```
new THREE.SpotLight(color, intensity, distance, angle, penumbra, decay)
```

其核心参数说明如下：

| 参数        | 类型   | 含义                                                                 | 默认值                      |
| ----------- | ------ | -------------------------------------------------------------------- | --------------------------- |
| `color`     | Color  | 光源颜色。                                                           | `0xffffff`（白色）          |
| `intensity` | Number | 光照强度（流明）。                                                   | `1`                         |
| `distance`  | Number | 光线照射的最大距离。当值为 `0` 时，光线可无限远。                    | `0`                         |
| `angle`     | Number | 光束的发散角度。                                                     | `Math.PI / 3`（默认 60 度） |
| `penumbra`  | Number | 光束边缘的柔和度，范围从 `0` 到 `1`。                                | `0`                         |
| `decay`     | Number | 光照强度随距离衰减的速率。在物理正确的渲染模式下，`decay` 应为 `2`。 | `2`                         |

#### 核心属性与方法

- **`.position`**: 类型为 `Vector3`。光源在三维空间中的位置。
- **`.target`**: 类型为 `Object3D`。聚光灯照射的目标对象，光束将指向此对象的位置。
- **`.angle`**: 控制光束的发散角度。较小的角度会产生更聚焦的光束，适合远距离照明；较大的角度则会产生更宽泛的光照范围。
- **`.penumbra`**: 控制光束边缘的柔和度。值越大，光束边缘过渡越柔和；值为 `0` 时，光束边缘锐利。
- **`.distance`**: 控制光线的有效范围。
- **`.decay`**: 定义光强如何随距离衰减。

#### 注意事项

- 聚光灯的阴影计算相对复杂，因为它需要根据光束的角度和距离来精确计算阴影的形状和边缘。
- `angle` 和 `penumbra` 属性可以组合使用，创造出各种光束形状和柔和度的效果。

#### 官方文档参考

- [Three.js SpotLight Documentation](https://threejs.org/docs/#api/zh/lights/SpotLight)

### 代码示例

<details>
<summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js 点光源与聚光灯示例</title>
    <style>
      body {
        margin: 0;
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
      import * as THREE from "three";
      import { OrbitControls } from "three/addons/controls/OrbitControls.js";

      // 1. 初始化场景、相机和渲染器
      const scene = new THREE.Scene();
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.set(4, 5, 10);

      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      // 开启阴影渲染
      renderer.shadowMap.enabled = true;
      document.body.appendChild(renderer.domElement);

      // 2. 添加轨道控制器
      const controls = new OrbitControls(camera, renderer.domElement);
      controls.enableDamping = true;

      // 3. 创建基础物体（一个平面和一个球体）
      const planeGeometry = new THREE.PlaneGeometry(20, 20);
      const planeMaterial = new THREE.MeshStandardMaterial({ color: 0xcccccc });
      const plane = new THREE.Mesh(planeGeometry, planeMaterial);
      plane.rotation.x = -Math.PI / 2;
      plane.position.y = -1;
      // 平面接收阴影
      plane.receiveShadow = true;
      scene.add(plane);

      const sphereGeometry = new THREE.SphereGeometry(1, 32, 32);
      const sphereMaterial = new THREE.MeshStandardMaterial({
        color: 0x8888ff,
      });
      const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);
      sphere.position.y = 1;
      // 球体投射阴影
      sphere.castShadow = true;
      scene.add(sphere);

      // 4. 创建点光源 (PointLight)
      const pointLight = new THREE.PointLight(0xffffff, 2, 15, 2);
      pointLight.position.set(-5, 3, 2);
      // 点光源投射阴影
      pointLight.castShadow = true;
      scene.add(pointLight);

      // 添加点光源辅助对象，用于可视化光源位置和范围
      const pointLightHelper = new THREE.PointLightHelper(pointLight, 0.5);
      scene.add(pointLightHelper);

      // 5. 创建聚光灯 (SpotLight)
      const spotLight = new THREE.SpotLight(
        0xffcc00,
        10,
        20,
        Math.PI / 8,
        0.2,
        2
      );
      spotLight.position.set(5, 5, 2);
      // 聚光灯默认指向 (0,0,0)，此处我们让它指向球体
      spotLight.target = sphere;
      // 聚光灯投射阴影
      spotLight.castShadow = true;
      scene.add(spotLight);

      // 添加聚光灯辅助对象，用于可视化光锥
      const spotLightHelper = new THREE.SpotLightHelper(spotLight);
      scene.add(spotLightHelper);

      // 6. 动画循环
      function animate() {
        requestAnimationFrame(animate);
        controls.update();
        // 聚光灯辅助对象需要手动更新
        spotLightHelper.update();
        renderer.render(scene, camera);
      }

      // 7. 响应窗口大小变化
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

<iframe src="step2/point-spot-light/demo.html" width="100%" height="500"></iframe>
