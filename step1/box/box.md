在 Three.js 中，场景中所有可见的物体都由一个核心对象 `Mesh` (网格) 来表示。`Mesh` 是构成三维世界的基本单元，它由两部分关键数据构成：**几何体 (Geometry)** 和 **材质 (Material)**。

几何体定义了物体的形状结构，而材质则决定了其表面的外观属性。要成功渲染一个物体，必须将这两者结合起来。

### 核心概念讲解

#### 定义与用途

几何体 (Geometry) 是一组用于定义三维物体形状的数据集合，其核心是顶点 (Vertices) 数据。

在现代 Three.js (r125+) 中，所有几何体都默认使用 `BufferGeometry`。这是一种高效的几何体表示方式，它将所有顶点相关的数据（如位置、法线、颜色和 UV 坐标）存储在缓冲区 (Buffer) 中，以便直接、快速地提交给 GPU 进行处理。

以创建一个立方体为例，需要使用 `BoxGeometry` 类。

#### 构造与参数

`BoxGeometry` 的构造函数如下：

`new THREE.BoxGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)`

其参数定义了立方体的尺寸和分段数。分段数越高，构成立方体每个面的三角形就越多。

| 参数             | 类型    | 含义             | 默认值 |
| ---------------- | ------- | ---------------- | ------ |
| `width`          | Float   | X 轴方向的长度   | 1      |
| `height`         | Float   | Y 轴方向的长度   | 1      |
| `depth`          | Float   | Z 轴方向的长度   | 1      |
| `widthSegments`  | Integer | X 轴方向的分段数 | 1      |
| `heightSegments` | Integer | Y 轴方向的分段数 | 1      |
| `depthSegments`  | Integer | Z 轴方向的分段数 | 1      |

#### 核心属性与方法

- **`.attributes`**: 一个包含几何体所有顶点属性的对象。最关键的属性是 `.attributes.position`，它是一个 `BufferAttribute`，存储了每个顶点的位置坐标 (x, y, z)。通过直接操作这些缓冲区数据，可以实现对几何体形状的高性能修改。

#### 注意事项

- 在 Three.js 中，一个几何体实例可以被多个 `Mesh` 对象复用，这有助于减少内存占用。
- 虽然 Three.js 提供了多种预设的几何体（如 `SphereGeometry`、`PlaneGeometry` 等），但复杂的模型通常由 3D 建模软件导出，并通过 `GLTFLoader` 等加载器载入。

#### 官方文档参考

- [Three.js BoxGeometry Documentation](https://threejs.org/docs/#api/zh/geometries/BoxGeometry "null")

### 基础材质 (MeshBasicMaterial)

#### 定义与用途

材质 (Material) 定义了物体表面的视觉特性。`MeshBasicMaterial` 是最基础的材质类型，它以一种简单的方式为几何体着色，其显示效果不受场景中光照的影响。这使得它非常适合用于渲染线框 (wireframe) 或在没有设置光源的场景中测试几何体。

#### 构造与参数

`MeshBasicMaterial` 的构造函数接受一个可选的参数对象：

`new THREE.MeshBasicMaterial(parameters)`

`parameters` 是一个包含材质属性键值对的对象。

#### 核心属性与方法

- **`.color`**: 材质的颜色，类型为 `Color`。在实例化时，可以通过一个十六进制数值来设置，例如 `{ color: 0xff0000 }` 表示红色。实例化后，需要通过 `.set()` 方法修改，例如 `material.color.set(0x00ff00)`。
- **`.wireframe`**: 布尔值。如果设置为 `true`，几何体将以线框模式渲染，而不是实心面。默认值为 `false`。
- **`.visible`**: 布尔值。设置为 `false` 时，使用此材质的物体将不会被渲染。默认值为 `true`。

#### 注意事项

- `MeshBasicMaterial` 不会对光照产生反应。如果需要创建具有明暗、阴影等真实感的物体，则必须使用其他材质，如 `MeshStandardMaterial` 或 `MeshLambertMaterial`，并配合场景中的光源使用。

#### 官方文档参考

- [Three.js MeshBasicMaterial Documentation](https://threejs.org/docs/#api/zh/materials/MeshBasicMaterial "null")

### 代码示例

以下代码将创建一个完整的 Three.js 场景，并在其中放置一个红色立方体。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```js
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Three.js 第一个物体</title>
    <style>
        body { margin: 0; }
        canvas { display: block; }
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
        // 导入 Three.js 核心库
        import * as THREE from 'three';

        // 1. 创建场景 (Scene)
        // 场景是所有三维对象的容器
        const scene = new THREE.Scene();

        // 2. 创建相机 (Camera)
        // PerspectiveCamera (透视相机) 用于模拟人眼视觉
        // 参数: 视野角度(FOV), 宽高比(aspect ratio), 近裁剪面(near), 远裁剪面(far)
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5; // 将相机向后移动，以便观察物体

        // 3. 创建渲染器 (Renderer)
        // WebGLRenderer 使用 WebGL 来在 <canvas> 上绘制场景
        const renderer = new THREE.WebGLRenderer();
        renderer.setSize(window.innerWidth, window.innerHeight); // 设置渲染尺寸
        document.body.appendChild(renderer.domElement); // 将渲染器的 canvas 元素添加到页面中

        // 4. 创建几何体 (Geometry)
        // BoxGeometry 用于创建一个长方体
        // 参数: width, height, depth
        const geometry = new THREE.BoxGeometry(1, 1, 1);

        // 5. 创建材质 (Material)
        // MeshBasicMaterial 是一种不受光照影响的基础材质
        // 参数: 一个包含材质属性的对象，这里设置颜色为红色 (0xff0000)
        const material = new THREE.MeshBasicMaterial({ color: 0xff0000 });

        // 6. 创建网格 (Mesh)
        // 网格对象由几何体和材质构成
        const cube = new THREE.Mesh(geometry, material);

        // 7. 将网格添加到场景中
        scene.add(cube);

        // 使用相机渲染场景
        renderer.render(scene, camera);
    </script>
</body>
</html>
```

</details>
<iframe src="step1/box/demo.html" width="100%" height="400"></iframe>

#### 实现逻辑说明

1. **初始化**: 首先，代码创建了渲染一个三维场景所必需的三个核心组件：`Scene`、`PerspectiveCamera` 和 `WebGLRenderer`。
2. **创建立方体**: 通过 `new THREE.BoxGeometry(1, 1, 1)` 创建了一个 1x1x1 大小的立方体几何体。
3. **定义外观**: 使用 `new THREE.MeshBasicMaterial({ color: 0xff0000 })` 创建了一个纯红色的基础材质。
4. **组合成 Mesh**: 将几何体和材质传入 `THREE.Mesh` 的构造函数，生成了最终的 `cube` 对象。

### 总结

本文详细阐述了在 Three.js 中构成一个可见物体的两个基本要素：定义形状的 **几何体 (Geometry)** 和定义外观的 **材质 (Material)** 。通过组合 `BoxGeometry` 和 `MeshBasicMaterial`，我们成功创建并渲染了第一个三维对象。
