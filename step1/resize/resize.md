在 Three.js 中，**自适应布局**是指调整渲染器（Renderer）和相机（Camera）的参数，使 3D 场景能够自动响应并填满整个浏览器可视区域的过程。

一个静态尺寸的 3D 场景在不同分辨率的设备上会产生滚动条或留白，严重影响用户体验。实现自适应布局是构建专业、沉浸式 Web3D 应用的基础，它确保了视觉呈现的一致性和完整性。要实现这一目标，需要处理两个核心环节：更新渲染尺寸和调整相机视角。

![](image/reset.png)

### 核心概念讲解

自适应布局的实现主要依赖于监听浏览器窗口的变化，并据此同步更新渲染器的大小和相机的宽高比。

#### **1. 更新渲染器尺寸**

- **定义与用途**: 渲染器（`WebGLRenderer`）负责将场景绘制到 HTML 的 `<canvas>` 元素上。为了让画布填满窗口，必须将渲染器的尺寸设置为与窗口的内部尺寸（`window.innerWidth` 和 `window.innerHeight`）一致。
- **核心方法**: `renderer.setSize(width, height, updateStyle)`
  - `width`: 画布的新宽度。
  - `height`: 画布的新高度。
  - `updateStyle` (可选): 一个布尔值。当设置为 `false` 时，`setSize` 将不会修改 `<canvas>` 元素的 CSS 样式。默认为 `true`。在大多数自适应场景中，保持默认值即可。
- **官方文档**: [THREE.WebGLRenderer](https://www.google.com/search?q=https://threejs.org/docs/%23api/zh/renderers/WebGLRenderer.setSize "null")

#### **2. 更新相机宽高比**

- **定义与用途**: 对于透视相机（`PerspectiveCamera`）而言，宽高比（`aspect`）决定了其视锥体的形状，直接影响场景的最终呈现。如果只改变渲染器的尺寸而不更新相机的宽高比，场景中的物体将会被不成比例地拉伸或压缩，导致失真。
- **核心属性**: `camera.aspect`
  - 该属性应始终设置为渲染区域的宽度除以高度。
- **核心方法**: `camera.updateProjectionMatrix()`
  - 在修改了相机的 `aspect` 等影响投影的属性后，**必须**调用此方法来使变更生效。Three.js 不会自动更新相机的投影矩阵。
- **官方文档**: [THREE.PerspectiveCamera](https://threejs.org/docs/#api/zh/cameras/PerspectiveCamera.updateProjectionMatrix "null")

#### **3. 监听浏览器窗口变化**

- **定义与用途**: 为了实现动态自适应，需要使用 JavaScript 的 `window.addEventListener` 来监听 `resize` 事件。当用户调整浏览器窗口大小时，该事件会被触发。
- **实现方式**: 在 `resize` 事件的回调函数中，执行更新渲染器尺寸和相机宽高比的全部操作。

#### **注意事项**

- **矩阵更新**: 切勿忘记在更新相机宽高比后调用 `camera.updateProjectionMatrix()`。这是初学者最常遗漏的步骤之一。

  ![](image/resize1.png)

### 代码示例

以下代码创建了一个包含单个立方体的基础场景。当浏览器窗口大小改变时，场景会自动调整以适应新的尺寸，且立方体不会变形。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js 自适应布局示例</title>
    <style>
      /* 移除 body 的默认边距，确保 canvas 能完全铺满 */
      body {
        margin: 0;
        overflow: hidden; /* 隐藏滚动条 */
      }
      /* 让 canvas 元素占据整个视口 */
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
        75, // 视野角度 (FOV)
        window.innerWidth / window.innerHeight, // 宽高比 (aspect ratio)
        0.1, // 近截面 (near)
        1000 // 远截面 (far)
      );
      camera.position.z = 5; // 将相机向后移动，以便观察物体

      const renderer = new THREE.WebGLRenderer({ antialias: true }); // 开启抗锯齿
      // 初始化时就设置渲染器尺寸为窗口大小
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement); // 将 canvas 添加到 body

      // 2. 创建一个物体并添加到场景中
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 }); // 使用标准材质以响应光照
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);

      // 添加一个简单的环境光
      const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
      scene.add(ambientLight);

      // 添加一个平行光，让立方体有明暗对比
      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(1, 1, 1);
      scene.add(directionalLight);

      // 3. 定义窗口大小调整的处理函数
      function onWindowResize() {
        // 更新相机的宽高比
        camera.aspect = window.innerWidth / window.innerHeight;
        // 更新相机的投影矩阵
        camera.updateProjectionMatrix();

        // 更新渲染器的尺寸
        renderer.setSize(window.innerWidth, window.innerHeight);
      }

      // 4. 监听窗口的 resize 事件
      window.addEventListener("resize", onWindowResize);

      // 5. 创建动画循环
      function animate() {
        requestAnimationFrame(animate);

        // 使立方体旋转，增加动态效果
        cube.rotation.x += 0.01;
        cube.rotation.y += 0.01;

        renderer.render(scene, camera);
      }

      // 启动动画
      animate();
    </script>
  </body>
</html>
```

</details>

![](./image/reset.gif)

#### 代码逻辑说明

1. **初始化**: 创建场景、相机和渲染器。关键在于，相机和渲染器的初始尺寸直接从 `window` 对象获取，确保了首次加载时场景就是全屏的。
2. **对象创建**: 创建一个简单的绿色立方体并将其添加到场景中，同时添加了基础光照，使其可见。
3. **`onWindowResize` 函数**: 这是实现自适应的核心。函数内部执行了两个关键操作：更新相机的 `aspect` 属性并调用 `updateProjectionMatrix()`，以及调用 `renderer.setSize()` 来调整画布大小。
4. **事件监听**: 通过 `window.addEventListener('resize', onWindowResize)` 绑定事件。现在，每当浏览器窗口尺寸发生变化，`onWindowResize` 函数就会被自动调用。
5. **动画循环**: `animate` 函数持续渲染场景，使得立方体的旋转动画能够被看到。
