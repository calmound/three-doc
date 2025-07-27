在正式学习 Three.js 之前，第一步是搭建一个稳定、可扩展的开发环境。本文将介绍三种常见方式，覆盖从入门到项目的不同需求：

- ✅ **快速上手**：直接通过 `<script>` 引入，零配置启动。
- ✅ **现代原生**：使用 `importmap`，无需构建工具即可享受模块化。
- ✅ **专业开发**：使用 Vite，搭建模块化、热更新的现代工程。

---

## 方法一：快速上手，HTML 直接引入

这是最简单直接的方式，非常适合初学者首次接触或快速验证 API 功能。

### 1.1 创建 HTML 文件

只需一个 `index.html` 文件即可。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Three.js Demo - Script Tag</title>
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
    <script src="https://unpkg.com/three@0.178.0/build/three.min.js"></script>

    <script>
      // 创建场景、相机、渲染器
      const scene = new THREE.Scene();
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      const renderer = new THREE.WebGLRenderer();
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // 添加立方体
      const geometry = new THREE.BoxGeometry(1, 1, 1);
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);

      camera.position.z = 5;

      function animate() {
        requestAnimationFrame(animate);
        cube.rotation.x += 0.01;
        cube.rotation.y += 0.01;
        renderer.render(scene, camera);
      }
      animate();
    </script>
  </body>
</html>
```

直接用浏览器打开此 HTML 文件即可看到效果。

### ✅ 优点：

- **零配置**：无需安装任何工具，即开即用。
- **直观易懂**：所有代码都在一个文件中，适合教学和演示。

### ⚠️ 缺点：

- **全局污染**：`THREE` 对象挂载在 `window` 上。
- **不支持模块化**：难以管理多文件结构，不适合复杂项目。
- **手动管理依赖**：如果需要轨道控制器等插件，需要手动引入额外的 JS 文件。

---

## 方法二：使用 Import Map，兼顾简洁与模块化

`importmap` 是一个现代浏览器原生支持的功能，它允许你在不使用打包工具的情况下，直接使用 ES 模块语法。这是介于 `<script>` 引入和完整构建流程之间的一个绝佳选择。

### 2.1 创建项目文件

创建 `index.html` 和 `main.js` 两个文件。

**`index.html`**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Three.js Demo - Import Map</title>
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
          "three": "https://unpkg.com/three@0.178.0/build/three.module.js",
          "three/addons/": "https://unpkg.com/three@0.178.0/examples/jsm/"
        }
      }
    </script>

    <script type="module" src="main.js"></script>
  </body>
</html>
```

**`main.js`** (注意：这里的代码和 Vite 中的几乎一样)

```js
import * as THREE from "three";
// 未来可以轻松地从 'three/addons/...' 引入控制器等
// import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshBasicMaterial({ color: 0xff00ff }); // 换个颜色区分
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

camera.position.z = 5;

function animate() {
  requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
}
animate();
```

### 2.2 启动开发环境

因为浏览器对 `file://` 协议下的模块加载有跨域限制（CORS），你需要一个本地服务器来运行。最简单的方式是使用 `serve`：

```bash
# 如果你没有安装 serve，可以通过 npx 直接运行
npx serve .
```

然后在浏览器中访问提示的地址（通常是 `http://localhost:3000`）。

### ✅ 优点：

- **无需构建**：没有复杂的配置和依赖安装。
- **现代语法**：可以像专业项目一样使用 `import`/`export`。
- **代码清晰**：轻松实现文件拆分和模块化管理。

### ⚠️ 缺点：

- **需要本地服务器**：不能直接双击打开 HTML 文件。
- **无打包优化**：浏览器会分别加载每个模块，在生产环境中可能不如打包后高效。
- **兼容性**：需要现代浏览器支持（目前主流浏览器均已支持）。

---

## 方法三：使用 Vite 搭建现代模块化开发环境

当你准备开发正式项目或希望代码组件化、工程化管理时，强烈建议使用 Vite。它提供了极速的冷启动、热模块替换（HMR）和丰富的插件生态。

### 3.1 初始化项目

```bash
# 创建一个基于原生 JS 的 Vite 项目
npm create vite@latest three-vite-app -- --template vanilla

# 进入项目目录并安装依赖
cd three-vite-app
npm install

# 安装 Three.js
npm install three
```

### 3.2 修改入口文件

编辑 `main.js`，内容和 `importmap` 示例几乎完全一样：

```js
// src/main.js
import * as THREE from "three";
import "./style.css"; // Vite 支持直接引入 CSS

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00aaff });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

camera.position.z = 5;

function animate() {
  requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
}
animate();
```

确保 `index.html` 的 `<script>` 标签类型为 `module` 并指向 `main.js`。

### 3.3 启动开发环境

```bash
npm run dev
```

浏览器访问 `http://localhost:5173`，你将看到一个旋转的立方体，并且任何代码改动都会即时反映在浏览器中，无需刷新。

### ✅ 优点：

- **开发体验极佳**：拥有闪电般的热更新（HMR）。
- **生态丰富**：支持 TypeScript、GLSL、资源优化等，插件众多。
- **生产力最高**：代码结构清晰，利于团队协作和大型项目维护。
- **自动优化**：`npm run build` 会打包、压缩、Tree Shaking，优化生产环境性能。

---

## 总结

三种方式各有千秋，如何选择取决于你的具体需求：

| 使用方式            | 适合场景                           | 优点                             | 不足之处                        |
| :------------------ | :--------------------------------- | :------------------------------- | :------------------------------ |
| **`<script>` 引入** | 快速入门、教学演示、代码片段验证   | 零配置、最简单、即开即用         | 不支持模块化，难以维护          |
| **`Import Map`**    | 快速原型、中小型项目、轻量级模块化 | 无需构建、支持原生模块、代码清晰 | 需本地服务器、无打包优化        |
| **Vite 模块开发**   | 正式项目、团队协作、长期维护       | 开发体验极佳、生态丰富、性能优化 | 需要 Node.js 环境和少量初期配置 |

本教程中，我们大部分案例使用方法 2，对于实战项目使用 vite 方式
