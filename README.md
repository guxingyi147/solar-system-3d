# 太阳系 · 三维导览

> 一个用 WebGL 写出来的交互式太阳系：真实轨道根数驱动、程序化生成的行星表面、可任意穿梭的 3D 视角。
> 单文件、零构建、零后端，打开浏览器即可运行。

**在线体验：** https://guxingyi147.github.io/solar-system-3d/
**源码仓库：** https://github.com/guxingyi147/solar-system-3d
**原始来源：** https://github.com/muxueliunian/solar-system-3d

> 本项目源自 [muxueliunian/solar-system-3d](https://github.com/muxueliunian/solar-system-3d)，
> 本仓库为其镜像归档，并补充了本文档。原始实现的全部权利归原作者所有。

---

## 一、项目简介

`solar-system-3d` 是一个纯前端的太阳系三维可视化项目。整个应用只有 **一个 `index.html`**（约 110 KB），
内联了全部 CSS 与 JavaScript，不需要 npm 安装、不需要打包构建、不需要服务器，双击即可打开。

它不是几张贴图拼起来的"示意图"，而是：

- 行星位置由 **J2000 平均轨道根数**实时解算，任意日期下的行星排布都与真实星历吻合；
- 行星表面（地球海陆、火星地貌、气态巨行星条带、月球环形山……）由 **GLSL 着色器程序化烘焙**生成，没有任何外部贴图文件；
- 使用 **Three.js + 后期处理管线**（UnrealBloom 辉光、HalfFloat 渲染目标、4× MSAA）呈现电影级观感。

---

## 二、功能特性

### 天体系统

| 类别 | 包含天体 |
| --- | --- |
| 恒星 | 太阳 |
| 类地行星 | 水星、金星、地球、火星 |
| 气态巨行星 | 木星（含大红斑）、土星（含北极六边形风暴） |
| 冰巨星 | 天王星、海王星 |
| 矮行星 | 冥王星 |
| 天然卫星 | 月球、火卫一/二、木卫一~四、土卫六、海卫一、冥卫一 |
| 区域标记 | 小行星带、柯伊伯带尘埃点云 |

共 **22 个可交互天体**，每个都配有中文介绍、参数表与冷知识。

### 交互能力

- **自由视角** — 拖拽旋转、滚轮缩放、右键平移，围绕任意天体观察
- **点击聚焦** — 点击天体或标签，相机平滑飞抵并弹出信息面板
- **底部 Dock** — 11 个主天体一键跳转，带真实配色的小星球图标
- **时间控制** — 时间流速从「分钟/秒」到「年/秒」连续可调，可暂停、可一键回到今天的真实行星位置
- **自动漫游** — 按 `T` 开启，相机依次巡访各大天体，配电影字幕式解说
- **轨道 / 标签 / 辉光** — 三个开关随时切换显示
- **画质档位** — 超 / 高 / 中 / 低 四档渲染分辨率，选择记入 `localStorage`
- **全屏模式** — 沉浸观赏

### 快捷键

| 按键 | 功能 |
| --- | --- |
| `0` – `9` | 快速切换：太阳→水星→金星→地球→火星→木星→土星→天王星→海王星→冥王星 |
| `空格` | 暂停 / 继续 |
| `Esc` | 回到总览视角 |
| `T` | 自动漫游开关 |
| `O` | 轨道显示开关 |
| `L` | 标签显示开关 |
| `H` | 隐藏 / 显示全部界面（纯净观赏模式） |
| `←` `→` | 上一个 / 下一个天体 |

### 视觉细节

- 太阳：日冕、活动区、米粒组织，配合 Bloom 辉光
- 地球：昼夜纹理、夜半球城市灯光、海陆分布、极地冰盖
- 气态行星：纬度条带 + 湍流扰动，木星自转最快故呈扁球形（`oblate` 参数）
- 土星 / 天王星：独立的光环体系，含 1D 噪声生成的环缝
- 大气层：各行星独立颜色与强度的辉光壳层
- 背景：程序化生成的星空点云 + 银河天球
- 移动端：≤860px 自动切换为紧凑布局

---

## 三、技术栈

| 项目 | 说明 |
| --- | --- |
| [Three.js](https://threejs.org/) `0.170.0` | WebGL 渲染核心，通过 importmap 从 jsDelivr CDN 引入 |
| OrbitControls | 相机轨道控制 |
| EffectComposer / RenderPass / UnrealBloomPass / OutputPass | 后期处理管线 |
| GLSL ES | 行星表面烘焙着色器、噪声函数、大气与光环着色 |
| 原生 HTML / CSS / JS | 无框架、无构建工具 |

**运行要求：** 支持 WebGL2 的现代浏览器（Chrome / Edge / Firefox / Safari 最新版）。
若初始化失败，页面会给出明确的错误提示而非白屏。

---

## 四、目录结构

```
solar-system-3d/
├── index.html      # 全部内容（HTML + CSS + JS + GLSL）
└── README.md
```

仓库里只有源码，没有构建配置、没有工作流、没有产物目录。

---

## 五、本地运行

由于使用了 ES Module 与 CDN importmap，需要通过 HTTP 访问（直接 `file://` 打开会被浏览器模块策略拦截）。
任选一种方式：

```bash
# Python
python -m http.server 8000

# Node
npx serve .
```

然后打开 http://localhost:8000 。

---

## 六、部署（GitHub Pages）

本项目是纯静态、零构建的——仓库根目录的 `index.html` 就是最终页面，不需要 GitHub Actions，也不需要 `gh-pages` 分支。

直接用 GitHub Pages 的**分支部署**即可：

1. 打开仓库 **Settings → Pages**；
2. **Build and deployment → Source** 选 **Deploy from a branch**；
3. **Branch** 选 `main`，目录选 `/ (root)`，保存。

几秒后即可访问 https://guxingyi147.github.io/solar-system-3d/ 。
之后每次 push 到 `main`，Pages 会自动更新。

> 若将来引入构建步骤（压缩、打包、生成多文件），再改用 GitHub Actions 工作流部署也不迟。

---

## 七、数据来源

- 行星轨道根数：J2000 平均轨道要素（半长径 `a`、偏心率 `e`、倾角 `i`、平黄经 `L`、近日点角距 `w`、升交点黄经 `Ω`、公转周期 `P`）
- 物理参数：NASA Planetary Fact Sheet 公开数据
- 表面纹理：全部由本项目 GLSL 着色器程序化生成，不依赖任何外部图片资源，无版权风险

---

## 八、来源与致谢

- **原始项目：** [muxueliunian/solar-system-3d](https://github.com/muxueliunian/solar-system-3d) —— 太阳系三维导览的全部实现（渲染、着色器、天体数据）均来自该项目，版权归原作者所有。
- **本仓库：** [guxingyi147/solar-system-3d](https://github.com/guxingyi147/solar-system-3d) —— 镜像归档，用于部署 GitHub Pages，改动仅为新增本 README 与调整目录结构。
- 若你是原作者且不希望此处保留该副本，请通过 issue 联系，我会立即删除。
