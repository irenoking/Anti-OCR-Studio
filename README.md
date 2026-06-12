# 🌊 Anti-OCR Studio (开放式流式叠加防 OCR 工作台)

[![Language](https://img.shields.io/badge/language-HTML5%20%2F%20JavaScript-yellow.svg)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Web%20%2F%20Cross--Platform-blue.svg)](#)

**Anti-OCR Studio** 是一款完全基于本地前端（HTML5 / Canvas）构建的开源隐私防护工作台。

本项目针对现代多模态大模型（VLM）及各类云端 OCR 引擎（如微信“图片大爆炸”）的底层视觉机制进行反向解构。通过**开放式插件架构（Plugin Architecture）**，允许用户自由将多种微观拓扑干涉、宏观几何切变算法加入**流式叠加管道（Pipeline Pipeline）**，在保持原图对肉眼 100% 干净、无害的前提下，使 AI 的特征提取与语义解码彻底瘫痪。

---

## ✨ 项目核心优势

1. **🔌 开放式插件化架构**：算法内核完全解耦。每个算法都是一个独立的 JS 文件，只需遵循统一规范即可动态加载。
2. **🎛️ 任意流式叠加（Pipeline）**：如同音频调音台，前一个插件的输出图像会自动成为下一个插件的输入，允许无限叠加对抗组合。
3. **⚡ 实时在线调参**：利用原生 Canvas 2D 硬件与软件缓存优化，拖入图片后拉动滑块，像素级变换毫秒级实时响应预览。
4. **🔒 100% 本地隐私安全**：纯前端矩阵计算，无任何后端服务器，零网络数据传输，截屏或敏感图片绝不泄露。
5. **📋 剪贴板原生互通**：支持现代浏览器 `ClipboardItem` 异步 DIB 读写，魔改完成后可一键复制回系统剪贴板，直接在社交软件中 `Ctrl + V` 发送。

---

## 🔬 硬核对抗原理

传统加粗线条、加刺眼噪声的防御极易被 AI 的低通滤波洗掉。本项目深入多模态模型的数学缺陷，提供多维度的降维打击：

- **宏观空间解构**：通过非线性局部剪切（Shear）、复合曲线扭曲，摧毁文本检测网络（如 DBNet）的**文本行矩形框选（Bounding Box）**，让 AI 定位失效。
- **微观拓扑粉碎**：利用多中心环形涟漪的径向拉扯与 **1 像素边缘梯度粉碎（Gradient Shatter）**，在不破坏宏观字形的前提下，斩断 AI 卷积核依赖的边缘梯度连续性，造成 Vision Transformer 视觉块的**特征混叠（Aliasing）**。
- **高层语义毒化**：将红蓝通道进行反向高频色相分离（Chroma Shuffling）或强行粘连显性特殊字符，直接污染大模型的高层自注意力特征向量（Embedding），引发**语言模型幻觉（Hallucination）**，使其吐出乱码。

---

## 📂 项目目录结构

```text
anti-ocr-studio/
│
├── index.html          # 主程序入口（UI 界面、核心调度引擎）
└── plugins/            # 开放式算法插件目录
    ├── curve_ripple.js     # 复合曲线波纹干涉
    ├── water_ripple.js     # 多中心环形流体干涉
    ├── shear_warp.js       # 文本行非线性局部剪切
    ├── gradient_shatter.js # 1像素微观边缘梯度粉碎
    ├── chroma_shuffling.js # 高频明相通道色相分离
    ├── physical_line.js    # 物理彩色线与几何斑块拦截
    └── text_poison.js      # 实体文字与符号语义污染

```

---

## 🚀 快速开始

本项目**不需要任何安装步骤**，也不依赖 Node.js 或任何后端环境。

### 1. 克隆仓库

```bash
git clone [https://github.com/irenoking/anti-ocr-studio.git](https://github.com/irenoking/anti-ocr-studio.git)
cd anti-ocr-studio

```

### 2. 运行工作台

直接双击运行主目录下的 `index.html`，或者将其拖入任意现代浏览器（Chrome、Edge、Safari、Firefox）即可。

### 3. 使用流控制台

1. 将需要保护的文字截图**拖拽**到右侧的工作区中。
2. 在左侧的**算法流控制台**中，按需开启不同的插件并调节滑块参数。
3. 调整满意后，点击 **`📋 复制最终魔改图`**，直接前往微信或钉钉聊天框粘贴发送。

---

## 🧱 扩展开发：如何编写自己的算法插件？

编写一个新插件极其简单。只需在 `plugins/` 目录下创建一个新的 `.js` 文件，并遵循以下解耦接口规范：

```javascript
// 初始化全局注册容器
if (!window.AntiOcrPlugins) window.AntiOcrPlugins = [];

window.AntiOcrPlugins.push({
    name: "🚀 你的硬核算法名称",
    
    // 1. 定义需要暴露给前端 UI 的滑块控件
    params: {
        myParam: { label: "参数显示名称", min: 0, max: 10, step: 0.5, value: 3 }
    },
    
    /**
     * 2. 核心流管道处理函数
     * @param {CanvasRenderingContext2D} ctx - 主画布上下文
     * @param {ImageData} imageData - 由上一个算法传入的中间像素切片
     * @param {Object} currentParams - 当前用户调参后的实时值
     * @returns {ImageData} 处理完成后的像素数据，将自动传给下一个插件
     */
    process: function(ctx, imageData, currentParams) {
        const w = imageData.width;
        const h = imageData.height;
        const src = imageData.data;
        
        const outputData = ctx.createImageData(w, h);
        const dst = outputData.data;
        
        // 在这里编写你的像素矩阵或图像处理算法...
        // 比如利用 currentParams.myParam.value 进行数学变换
        
        return outputData;
    }
});

```

编写完成后，只需在 `index.html` 的 `pluginFiles` 数组中加入该 JS 文件的路径，控制台便会自动识别并生成交互 UI。

---

## 📄 开源协议

本项目基于 **MIT License** 协议开源，详情请参阅 [LICENSE](https://www.google.com/search?q=LICENSE) 文件。

```


3. **丰富 Readme**：如果在 GitHub 仓库根目录下放一张你的工作台运行时的系统调参截图（包含左边丰富的滑块和右边清爽的文字效果），并在文档里加上 `![Preview](./preview.jpg)`，会更容易吸引别人给你点 **Star**！

```
