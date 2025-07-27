渲染器（Renderer）是 Three.js 的核心组件之一，其职责是获取由场景（Scene）和相机（Camera）共同定义的虚拟三维空间，并将其计算、绘制成最终在浏览器 `<canvas>` 元素上显示的二维图像。

在 Three.js 的工作流程中，所有三维对象、光源和相机都只是存在于内存中的数据结构。渲染器是连接这个抽象数据世界与用户可见屏幕的桥梁，没有它，任何内容都无法被看见。

要将一个三维场景呈现出来，首先需要实例化一个合适的渲染器。

### **核心概念讲解**

#### **定义与用途**

在 Three.js 中，最常用的渲染器是 `WebGLRenderer`。它利用 WebGL (Web Graphics Library) 技术，通过调用客户端的图形硬件（GPU）来进行高性能的三维图形渲染。其根本用途是将一个包含多个物体、光源和阴影等信息的三维场景图（Scene Graph），根据特定相机（Camera）的视角、位置和投影方式，计算出最终的像素颜色，并将其绘制到 HTML 的 `<canvas>` 元素上。

#### **构造与参数**

`WebGLRenderer` 通过其构造函数进行实例化。

**构造方法:** `new THREE.WebGLRenderer(parameters)`

`parameters` 是一个可选的对象，用于传入初始化参数。以下是其关键参数：

- **`canvas`**:
  - **类型**: `HTMLCanvasElement`
  - **含义**: 指定渲染器在其上绘制输出的 `<canvas>` 元素。如果未提供此参数，渲染器会自动创建一个新的 `<canvas>` 元素。
  - **默认值**: `undefined`
- **`antialias`**:
  - **类型**: `Boolean`
  - **含义**: 是否开启抗锯齿（或称“反走样”）。开启后，物体边缘的锯齿状线条会变得更平滑，但会消耗更多的性能。
  - **默认值**: `false`
- **`alpha`**:
  - **类型**: `Boolean`
  - **含义**: 控制画布是否包含 alpha (透明度) 通道。如果设置为 `true`，画布背景将是透明的，允许其下方的 HTML 内容透出。
  - **默认值**: `false`

#### **核心属性与方法**

- **`.domElement`**
  - **类型**: `HTMLCanvasElement`
  - **功能**: 这是渲染器用于绘制输出的 `<canvas>` 元素。在初始化渲染器后，需要将此 DOM 元素手动添加到页面的 HTML 结构中，才能让渲染结果可见。
- **`.setSize(width, height, updateStyle)`**
  - **功能**: 设置渲染输出区域（即 `<canvas>`）的尺寸。此方法应在初始化后调用，并在窗口大小改变时再次调用以确保渲染分辨率与显示区域匹配。
  - **参数**:
    - `width`: `Integer`，输出区域的宽度。
    - `height`: `Integer`，输出区域的高度。
    - `updateStyle`: `Boolean` (可选)，若设为 `false`，则仅更新画布的渲染缓冲区大小，而不改变其 CSS 样式。通常保持默认即可。
- **`.render(scene, camera)`**
  - **功能**: 执行单次渲染。此方法接收一个场景和一个相机作为参数，并根据它们的状态将场景渲染到画布上。在静态场景中，调用一次即可。在需要动画或交互的场景中，此方法需要在每一帧被重复调用。

#### **官方文档参考**

关于 `WebGLRenderer` 的更多详细信息，可查阅 Three.js 官方文档： [https://threejs.org/docs/#api/zh/renderers/WebGLRenderer](https://threejs.org/docs/#api/zh/renderers/WebGLRenderer "null")

#### **注意事项**

- **设备像素比**: 在高 DPI (High Dots Per Inch) 的屏幕（如 Retina 屏）上，为了获得更清晰的渲染效果，应调用 `.setPixelRatio(window.devicePixelRatio)` 方法，将渲染器的像素比设置为设备的物理像素比。
- **动画循环**: 对于动态场景，`.render()` 方法必须在一个循环中被持续调用，通常使用 `requestAnimationFrame` 来创建性能友好的动画循环。
- **资源释放**: 当不再需要渲染器时，可以调用其 `.dispose()` 方法来释放占用的 GPU 相关资源。

### **代码示例**

以下代码演示了创建一个 `WebGLRenderer` 并用它渲染一个简单场景的基本流程。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js 渲染器示例</title>
    <style>
      /* 移除 body 的默认边距，让 canvas 占满整个视口 */
      body {
        margin: 0;
      }
      /* 确保 canvas 块级显示，避免下方出现空隙 */
      canvas {
        display: block;
      }
    </style>
  </head>
  <body>
    <!-- Three.js 库的引入 -->
    <script type="importmap">
      {
        "imports": {
          "three": "https://unpkg.com/three@0.160.0/build/three.module.js"
        }
      }
    </script>

    <script type="module">
      // 从 'three' 模块中导入所需的核心类
      import * as THREE from "three";

      // 1. 创建场景 (Scene)
      // 场景是所有三维物体的容器
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0x111111); // 设置场景背景色为深灰色

      // 2. 创建相机 (Camera)
      // PerspectiveCamera (透视相机) 模拟人眼观察世界的方式
      // 参数: 视场角度(fov), 宽高比(aspect), 近裁剪面(near), 远裁剪面(far)
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.z = 5; // 将相机沿 Z 轴向后移动 5 个单位，以便能看到原点处的物体

      // 3. 创建一个可见物体 (Mesh)
      // 创建一个立方体的几何形状
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      // 创建一种基础材质，不受光照影响
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 }); // 绿色
      // 将几何体和材质结合成一个网格模型
      const cube = new THREE.Mesh(geometry, material);
      // 将立方体添加到场景中
      scene.add(cube);

      // 4. 创建渲染器 (Renderer)
      // 实例化 WebGL 渲染器，并开启抗锯齿以获得更平滑的边缘
      const renderer = new THREE.WebGLRenderer({ antialias: true });

      // 5. 设置渲染器的尺寸
      // 将渲染器的尺寸设置为浏览器窗口的内部宽高
      renderer.setSize(window.innerWidth, window.innerHeight);

      // 6. 将渲染器的 canvas 元素添加到 HTML 文档中
      // renderer.domElement 是一个 canvas 元素，渲染结果将绘制于此
      document.body.appendChild(renderer.domElement);

      // 7. 执行渲染
      // 调用 render 方法，传入场景和相机，将场景内容绘制到 canvas 上
      renderer.render(scene, camera);
    </script>
  </body>
</html>
```

</details>

<iframe src="step1/renderer/demo.html" width="100%" height="500"></iframe>
