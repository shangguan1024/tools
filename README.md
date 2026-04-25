# Layer 3D 可视化工具

基于 Three.js 的浏览器端 3D 可视化工具，解析文本日志中的 layer 信息，将每个 layer 渲染为实体方块，按日志顺序在 z 轴依次堆叠。

## 功能特性

- 3D 可视化显示 layer 层叠效果
- 每个 layer 显示为带 ID 标签的实体方块
- 支持鼠标交互：旋转、缩放、平移
- 文件选择器加载本地日志

## 日志格式

```
dump layer:
layer：id 1 x 10.0 y 10.0 x 20.0 y 20.0
layer：id 2 x 10.0 y 10.0 x 20.0 y 20.0
```

- `id`: layer 标识
- `x y`: 左上角坐标
- `x y`: 右下角坐标

## 使用方法

1. 直接在浏览器中打开 `index.html`
2. 点击"选择日志文件"按钮，选择日志文件
3. 使用鼠标交互查看 3D 效果：
   - 左键拖动：旋转视角
   - 滚轮：缩放
   - 右键拖动：平移

## 技术栈

- Three.js r160
- OrbitControls
- TextGeometry
