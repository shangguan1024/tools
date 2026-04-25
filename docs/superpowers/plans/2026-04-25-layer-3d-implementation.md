# Layer 3D 可视化工具实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 创建单HTML文件的3D可视化工具，解析日志并渲染带文字标签的layer方块

**Architecture:** Three.js单文件应用，CDN引入库，本地读取日志文件，渲染3D场景

**Tech Stack:** Three.js r160, OrbitControls, TextGeometry, FontLoader

---

### Task 1: 创建 index.html 基本结构

**Files:**
- Create: `index.html`

- [ ] **Step 1: 创建HTML骨架和样式**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Layer 3D 可视化工具</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { overflow: hidden; font-family: Arial, sans-serif; }
        #toolbar {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            height: 50px;
            background: #2c3e50;
            display: flex;
            align-items: center;
            padding: 0 20px;
            gap: 10px;
            z-index: 100;
        }
        #toolbar button {
            padding: 8px 16px;
            background: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
        }
        #toolbar button:hover { background: #2980b9; }
        #info {
            color: #ecf0f1;
            margin-left: 20px;
            font-size: 14px;
        }
        #canvas-container { width: 100vw; height: 100vh; }
    </style>
</head>
<body>
    <div id="toolbar">
        <button id="btn-load">读取日志</button>
        <button id="btn-reset">重置视角</button>
        <span id="info">等待加载日志...</span>
    </div>
    <div id="canvas-container"></div>
</body>
</html>
```

- [ ] **Step 2: 引入Three.js CDN**

在 `<head>` 中添加:

```html
<script type="importmap">
{
    "imports": {
        "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
        "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
    }
}
</script>
```

- [ ] **Step 3: 添加模块脚本**

在 `<body>` 末尾添加:

```html
<script type="module">
    import * as THREE from 'three';
    import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
    import { FontLoader } from 'three/addons/loaders/FontLoader.js';
    import { TextGeometry } from 'three/addons/geometries/TextGeometry.js';

    // 全局变量
    let scene, camera, renderer, controls;
    const layers = [];

    // 初始化
    function init() {
        // 场景
        scene = new THREE.Scene();
        scene.background = new THREE.Color(0xf5f5f5);

        // 相机
        camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.set(50, 50, 50);

        // 渲染器
        renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.getElementById('canvas-container').appendChild(renderer.domElement);

        // 轨道控制
        controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;

        // 灯光
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
        scene.add(ambientLight);
        const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
        directionalLight.position.set(50, 50, 50);
        scene.add(directionalLight);

        // 窗口调整
        window.addEventListener('resize', onWindowResize);

        // 按钮事件
        document.getElementById('btn-load').addEventListener('click', loadLog);
        document.getElementById('btn-reset').addEventListener('click', resetCamera);

        // 渲染循环
        animate();
    }

    function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    function animate() {
        requestAnimationFrame(animate);
        controls.update();
        renderer.render(scene, camera);
    }

    function resetCamera() {
        camera.position.set(50, 50, 50);
        camera.lookAt(0, 0, 0);
        controls.reset();
    }

    init();
</script>
```

- [ ] **Step 4: 创建日志解析和渲染函数（待实现）**

在 `init()` 前添加空函数:

```javascript
    async function loadLog() {
        // TODO: 实现日志加载
    }

    function clearLayers() {
        // TODO: 清空现有layers
    }

    function createLayerBox(id, x1, y1, x2, y2, z) {
        // TODO: 创建带文字标签的方块
    }
```

- [ ] **Step 5: 保存文件**

---

### Task 2: 实现日志解析和加载功能

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 实现 loadLog 函数**

找到 `async function loadLog()` 用以下代码替换:

```javascript
    async function loadLog() {
        try {
            const response = await fetch('layer_log.txt');
            const text = await response.text();
            parseAndRender(text);
            document.getElementById('info').textContent = '日志加载成功';
        } catch (error) {
            document.getElementById('info').textContent = '加载失败: ' + error.message;
        }
    }
```

- [ ] **Step 2: 实现 parseAndRender 函数**

在 `loadLog` 函数前添加:

```javascript
    function parseAndRender(text) {
        clearLayers();
        
        const lines = text.split('\n');
        let layerIndex = 0;
        
        for (const line of lines) {
            const trimmed = line.trim();
            if (!trimmed || !trimmed.startsWith('layer：')) continue;
            
            const parts = trimmed.replace('layer：', '').split(/\s+/);
            let id = '', x1, y1, x2, y2;
            
            for (let i = 0; i < parts.length; i++) {
                if (parts[i] === 'id' && i + 1 < parts.length) {
                    id = parts[i + 1];
                }
                if (parts[i] === 'x' && i + 1 < parts.length) {
                    if (x1 === undefined) x1 = parseFloat(parts[i + 1]);
                    else if (x2 === undefined) x2 = parseFloat(parts[i + 1]);
                }
                if (parts[i] === 'y' && i + 1 < parts.length) {
                    if (y1 === undefined) y1 = parseFloat(parts[i + 1]);
                    else if (y2 === undefined) y2 = parseFloat(parts[i + 1]);
                }
            }
            
            if (id && x1 !== undefined && y1 !== undefined && x2 !== undefined && y2 !== undefined) {
                createLayerBox(id, x1, y1, x2, y2, layerIndex);
                layerIndex++;
            }
        }
        
        document.getElementById('info').textContent = `已加载 ${layerIndex} 个layer`;
    }
```

- [ ] **Step 3: 实现 clearLayers 函数**

找到 `function clearLayers()` 用以下代码替换:

```javascript
    function clearLayers() {
        for (const layer of layers) {
            scene.remove(layer.box);
            scene.remove(layer.text);
        }
        layers.length = 0;
    }
```

- [ ] **Step 4: 测试 - 验证基本渲染**

打开 index.html，确认控制台无错误。

---

### Task 3: 实现3D方块和文字标签渲染

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 加载字体**

在 `init()` 函数内的灯光代码后添加:

```javascript
        const fontLoader = new FontLoader();
        fontLoader.load('https://unpkg.com/three@0.160.0/examples/fonts/helvetiker_bold.typeface.json', (font) => {
            window.textFont = font;
        });
```

- [ ] **Step 2: 实现 createLayerBox 函数**

找到 `function createLayerBox(id, x1, y1, x2, y2, z)` 用以下代码替换:

```javascript
    function createLayerBox(id, x1, y1, x2, y2, z) {
        const width = Math.abs(x2 - x1);
        const height = Math.abs(y2 - y1);
        const depth = 1;
        
        const centerX = (x1 + x2) / 2;
        const centerY = (y1 + y2) / 2;
        
        const geometry = new THREE.BoxGeometry(width, height, depth);
        const material = new THREE.MeshPhongMaterial({ 
            color: 0x7f8c8d,
            transparent: true,
            opacity: 0.9
        });
        const box = new THREE.Mesh(geometry, material);
        box.position.set(centerX, centerY, z * 2);
        scene.add(box);
        
        let textMesh = null;
        if (window.textFont) {
            const textGeometry = new TextGeometry(id, {
                font: window.textFont,
                size: Math.min(width, height) * 0.4,
                height: 0.1
            });
            textGeometry.computeBoundingBox();
            const textMaterial = new THREE.MeshPhongMaterial({ color: 0xffffff });
            textMesh = new THREE.Mesh(textGeometry, textMaterial);
            
            const bbox = textGeometry.boundingBox;
            const textWidth = bbox.max.x - bbox.min.x;
            const textHeight = bbox.max.y - bbox.min.y;
            
            textMesh.position.set(centerX - textWidth / 2, centerY - textHeight / 2, z * 2 + depth / 2 + 0.1);
            scene.add(textMesh);
        }
        
        layers.push({ box, text: textMesh, id });
    }
```

- [ ] **Step 3: 测试 - 验证方块和文字渲染**

打开 index.html，点击"读取日志"，验证方块和ID文字是否正确显示。

---

### Task 4: 添加坐标轴辅助线和调试

**Files:**
- Modify: `index.html`

- [ ] **Step 1: 添加坐标轴辅助**

在 `init()` 函数中添加灯光代码后:

```javascript
        const axesHelper = new THREE.AxesHelper(100);
        scene.add(axesHelper);
        
        const gridHelper = new THREE.GridHelper(200, 20);
        scene.add(gridHelper);
```

- [ ] **Step 2: 测试并优化**

验证坐标轴显示正常，z轴层叠效果可见。

---

### Task 5: 最终验证

- [ ] **Step 1: 在浏览器中完整测试**

1. 打开 index.html
2. 点击"读取日志"按钮
3. 验证：
   - 两个方块正确渲染
   - 层叠顺序正确（z轴）
   - ID文字标签显示
   - 鼠标旋转、缩放功能正常

- [ ] **Step 2: 提交代码**

```bash
git add index.html layer_log.txt docs/
git commit -m "feat: 实现Layer 3D可视化工具"
```

---

## 验证清单

- [ ] 日志解析正确识别 id, x1, y1, x2, y2
- [ ] 方块尺寸与坐标一致
- [ ] z轴层叠顺序正确
- [ ] ID文字标签可读
- [ ] 鼠标交互流畅
