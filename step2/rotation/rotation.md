旋转（Rotation）是三维空间中的一种基本变换，它定义了物体围绕某个点或某条轴线的方向状态。

在 Three.js 中，精确控制物体的旋转是构建动态、交互式三维场景的核心环节，无论是让行星沿轨道转动，还是调整相机的观察视角，都离不开旋转操作。为了描述和计算旋转，三维图形学提供了多种数学工具，其中最主要的是欧拉角（Euler Angles）和四元数（Quaternions）。

### 核心概念讲解：欧拉角

欧拉角是一种非常直观的旋转表示方法，它将一个复杂的旋转分解为绕三个主轴（X、Y、Z）连续进行的三次独立旋转。

**定义与用途**

`THREE.Euler` 类用于表示欧拉角。由于其将旋转分解到三个独立的分量上，因此非常易于理解和调试。在 Three.js 中，所有 `Object3D` 对象都拥有一个 `.rotation` 属性，它就是一个 `Euler` 对象，允许直接修改其 `x`、`y`、`z` 值来控制物体的旋转。

**构造与参数**

构造函数: `Euler(x, y, z, order)`

| 参数    | 类型   | 含义                                                             | 默认值  |
| ------- | ------ | ---------------------------------------------------------------- | ------- |
| `x`     | Float  | 绕 X 轴的旋转角度，以弧度为单位。                                | `0`     |
| `y`     | Float  | 绕 Y 轴的旋转角度，以弧度为单位。                                | `0`     |
| `z`     | Float  | 绕 Z 轴的旋转角度，以弧度为单位。                                | `0`     |
| `order` | String | 旋转应用顺序。例如 'XYZ' 表示先绕 X 轴，再绕 Y 轴，最后绕 Z 轴。 | `'XYZ'` |

**核心属性与方法**

- **.x, .y, .z**: 直接访问和修改绕各轴的旋转角度（弧度）。
- **.set(x, y, z, order)**: 一次性设置三个分量和旋转顺序。这是最常用的方法，可以确保原子性更新。

**官方文档参考**

- [Three.js Euler API 文档](https://threejs.org/docs/#api/zh/math/Euler)

**注意事项**

欧拉角存在一个固有的问题，称为“万向节死锁”（Gimbal Lock）。当某个轴的旋转角度为 ±90 度时，会导致另外两个轴的旋转轴重合，从而丢失一个旋转自由度。这会使得在某些姿态下的旋转操作变得不符合预期。因此，对于需要进行复杂或连续动画的旋转，推荐使用四元数。

### 核心概念讲解：四元数

四元数是一种更复杂的数学概念，用于在三维空间中表示旋转。它能有效避免万向节死锁问题。

**定义与用途**

`THREE.Quaternion` 类用于表示四元数。它由四个分量（x, y, z, w）构成，能够平滑、高效地插值，并避免万向节死锁。在 Three.js 内部，所有物体的旋转最终都由四元数（存储在 `.quaternion` 属性中）来处理，即使修改的是 `.rotation` 欧拉角，其内部也会自动更新对应的四元数。

**构造与参数**

构造函数: `Quaternion(x, y, z, w)`

| 参数 | 类型  | 含义                                        | 默认值 |
| ---- | ----- | ------------------------------------------- | ------ |
| `x`  | Float | 四元数的 x 分量。                           | `0`    |
| `y`  | Float | 四元数的 y 分量。                           | `0`    |
| `z`  | Float | 四元数的 z 分量。                           | `0`    |
| `w`  | Float | 四元数的 w 分量，通常表示旋转角度的余弦值。 | `1`    |

**核心属性与方法**

- **.x, .y, .z, .w**: 直接访问和修改四元数的各个分量。
- **.set(x, y, z, w)**: 一次性设置四元数的所有分量。
- **.multiply(quaternion)**: 将当前四元数与另一个四元数相乘，结果存储在当前四元数中。这用于组合旋转。
- **.invert()**: 计算当前四元数的逆四元数，结果存储在当前四元数中。逆四元数表示相反的旋转。
- **.conjugate()**: 计算当前四元数的共轭四元数，结果存储在当前四元数中。共轭四元数的虚部取反。
- **.normalize()**: 将四元数归一化，使其长度为 1。这对于确保旋转的正确性非常重要。

**官方文档参考**

- [Three.js Quaternion API 文档](https://threejs.org/docs/#api/zh/math/Quaternion)

**注意事项**

四元数虽然能够有效避免万向节死锁问题，但其直观性不如欧拉角。四元数的分量没有直接的几何意义，理解和调试相对困难。此外，四元数的插值（如使用 Slerp 方法）虽然能够产生平滑的旋转过渡，但在某些情况下可能会出现非线性的旋转速度变化。因此，在使用四元数进行旋转时，需要额外注意这些细节问题。

### 代码示例

以下示例创建了一个立方体，并允许通过修改其 .rotation 属性（一个 Euler 对象）来实时观察物体的旋转。Three.js 会在内部自动将这个欧拉角转换为四元数来应用变换。

<details>
<summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js 旋转示例</title>
    <style>
      body {
        margin: 0;
        overflow: hidden;
      }
      canvas {
        display: block;
      }
      .dg.main {
        position: absolute;
        top: 10px;
        right: 10px;
      }
    </style>
  </head>
  <body>
    <script type="importmap">
      {
        "imports": {
          "three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js",
          "three/addons/": "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/"
        }
      }
    </script>
    <script type="module">
      import * as THREE from "three";
      import { OrbitControls } from "three/addons/controls/OrbitControls.js";
      import { GUI } from "https://unpkg.com/dat.gui@0.7.9/build/dat.gui.module.js";

      // 1. 初始化场景、相机和渲染器
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0x111111);

      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.set(3, 4, 5);
      camera.lookAt(scene.position);

      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // 2. 添加辅助对象
      // 轨道控制器，允许用鼠标交互
      const controls = new OrbitControls(camera, renderer.domElement);
      // 坐标轴辅助线
      const axesHelper = new THREE.AxesHelper(5);
      scene.add(axesHelper);
      // 环境光
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      scene.add(ambientLight);
      // 平行光
      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(5, 5, 5);
      scene.add(directionalLight);

      // 3. 创建核心对象：一个立方体
      const geometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);
      const material = new THREE.MeshStandardMaterial({ color: 0x0099ff });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);

      // 4. GUI 控制面板
      // 创建一个对象来存储需要通过 GUI 控制的属性
      const rotationControls = {
        rotationX: 0,
        rotationY: 0,
        rotationZ: 0,
      };

      const gui = new GUI();
      // 添加滑块来控制绕 X, Y, Z 轴的旋转
      // THREE.MathUtils.degToRad 用于将角度转换为弧度
      gui
        .add(rotationControls, "rotationX", -180, 180)
        .name("绕 X 轴旋转")
        .onChange(updateRotation);
      gui
        .add(rotationControls, "rotationY", -180, 180)
        .name("绕 Y 轴旋转")
        .onChange(updateRotation);
      gui
        .add(rotationControls, "rotationZ", -180, 180)
        .name("绕 Z 轴旋转")
        .onChange(updateRotation);

      // 5. 更新旋转的函数
      function updateRotation() {
        // cube.rotation 是一个 Euler 对象
        // 当 GUI 滑块变化时，用新值来更新立方体的 rotation 属性
        // 注意：dat.gui 提供的是角度值，需要转换为 Three.js 使用的弧度值
        cube.rotation.x = THREE.MathUtils.degToRad(rotationControls.rotationX);
        cube.rotation.y = THREE.MathUtils.degToRad(rotationControls.rotationY);
        cube.rotation.z = THREE.MathUtils.degToRad(rotationControls.rotationZ);
      }

      // 6. 渲染循环
      function animate() {
        requestAnimationFrame(animate);
        controls.update(); // 更新轨道控制器
        renderer.render(scene, camera);
      }

      // 7. 响应窗口大小变化
      window.addEventListener("resize", () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      });

      // 启动渲染
      animate();
      // 初始化一次旋转状态
      updateRotation();
    </script>
  </body>
</html>

```

</details>

<iframe src="step2/transform-position-scale/demo.html" width="100%" height="500"></iframe>
