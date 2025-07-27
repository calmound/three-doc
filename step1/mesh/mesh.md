在 Three.js 中，网格 (Mesh) 是一个基本对象，它代表了场景中一个具体的、可见的物体。它由两部分构成：几何体 (Geometry) 定义了物体的形状，而材质 (Material) 则定义了物体的外观，例如颜色、纹理或对光照的反应。

没有网格，几何体和材质本身无法被渲染到场景中。它是将抽象的形状数据和外观属性结合起来，并放置到三维空间中的最终承载者。要创建一个可见的物体，就需要理解网格的构造和使用方式。

### 核心概念讲解

#### 定义与用途

网格 (Mesh) 是一个包含几何体和材质的类，用于在场景中绘制三维对象的表面。当一个 `Mesh` 对象被创建后，它可以被添加到 `Scene` 对象中，并通过渲染器 (`Renderer`) 在屏幕上绘制出来。从简单的立方体、球体，到通过外部软件导入的复杂模型，场景中几乎所有可见的实体都是以网格的形式存在的。

#### 构造与参数

网格通过其构造函数 `new THREE.Mesh()` 来实例化。

`new` THREE.Mesh( geometry, material )

| 参数       | 类型           | 含义                                                                            | 默认值                          |
| ---------- | -------------- | ------------------------------------------------------------------------------- | ------------------------------- |
| `geometry` | BufferGeometry | (可选) 一个 `BufferGeometry` 或其派生类的实例，定义了物体的顶点、面等形状数据。 | `new THREE.BufferGeometry()`    |
| `material` | Material       | (可选) 一个 `Material` 或其派生类的实例，定义了物体的表面属性。                 | `new THREE.MeshBasicMaterial()` |

#### 核心属性与方法

由于 `Mesh` 继承自 `Object3D`，它拥有所有用于变换（移动、旋转、缩放）的属性。

1. **.position**:

   - **类型**: `Vector3`
   - **功能**: 定义了网格对象在三维空间中的局部坐标位置。可以通过修改其 `.x`, `.y`, `.z` 分量来移动物体。

2. **.rotation**:

   - **类型**: `Euler`
   - **功能**: 定义了网格对象围绕自身坐标轴的旋转状态，以弧度为单位。可以通过修改其 `.x`, `.y`, `.z` 分量来旋转物体。

3. **.scale**:

   - **类型**: `Vector3`
   - **功能**: 定义了网格对象的局部缩放比例。默认值为 `(1, 1, 1)`。可以通过修改其 `.x`, `.y`, `.z` 分量来沿相应轴向缩放物体。

#### 官方文档参考

更详细的 API 信息，请查阅 Three.js 官方文档：

- [Mesh API 文档 (中文)](https://threejs.org/docs/#api/zh/objects/Mesh "null")

#### 注意事项

- 一个网格对象通常只接收一个几何体和一个材质。若要对一个物体的不同部分应用不同材质，需要使用材质数组，并确保几何体已正确设置了分组 (groups)。
- 所有网格对象都继承自 `Object3D`，因此天然具备了位置、旋转和缩放等变换属性，可以直接操作这些属性来控制物体在场景中的状态。

### 代码示例

以下代码将创建一个基础的场景，并在其中放置一个彩色的、不断旋转的立方体。这个立方体就是一个 `Mesh` 对象。

<details>
  <summary style="color: #fff;background:#3992e6;padding: 4px;width: 120px;cursor:pointer;">点击展开代码</summary>

```js
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Three.js 网格(Mesh)示例</title>
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
        // 导入 Three.js
        import * as THREE from 'three';

        // 1. 初始化场景 (Scene)
        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x111111); // 设置场景背景色

        // 2. 初始化相机 (Camera)
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 5; // 将相机向后移动，以便观察物体

        // 3. 初始化渲染器 (Renderer)
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        // --- 核心步骤：创建网格 (Mesh) ---

        // 4. 创建几何体 (Geometry) - 定义物体的形状
        // 创建一个 1x1x1 的立方体几何体
        const geometry = new THREE.BoxGeometry(1.5, 1.5, 1.5);

        // 5. 创建材质 (Material) - 定义物体的外观
        // 创建一个基础网格材质，并设置颜色为橙色
        const material = new THREE.MeshBasicMaterial({ color: 0xffa500 });

        // 6. 创建网格 (Mesh) - 将几何体和材质组合成一个物体
        const cube = new THREE.Mesh(geometry, material);

        // 7. 将网格添加到场景中
        scene.add(cube);
        // 渲染场景
        renderer.render(scene, camera);
    </script>
</body>
</html>
```

</details>
<iframe src="step1/mesh/demo.html" width="100%" height="400"></iframe>
