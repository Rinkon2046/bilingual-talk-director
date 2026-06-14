---
name: bilingual-talk-director
description: >-
  用 HyperFrames(HeyGen 开源、写 HTML 渲染视频)制作"车内口播 / talking-head 双语强字幕"风格的竖版短视频。
  适用场景:把一段口播录像(出镜讲解)配上①顶部章节进度条 ②底部双语字幕(中文加粗大字+关键词橙色高亮,
  英文等宽小字译文)③暗色圆角引用/产品卡片(带来源标签与 pill 标记)④强调大标题。当用户要"做成那种小红书/竖屏
  口播带双语字幕和章节进度条的视频""复刻某口播视频的字幕风格""用 HyperFrames 出口播视频"时使用本技能。
license: Apache-2.0
version: 1.0.0
---

# 双语口播导演(HyperFrames)

本技能教你用 **HyperFrames** 复刻一种口播短视频风格:满屏出镜画面 + 顶部章节进度条 +
底部双语字幕(中文大字、关键词橙色高亮、英文等宽译文)+ 暗色圆角引用/产品卡片。
产物是带 `data-*` 时间属性的 HTML 合成文件,用 `npx hyperframes preview/render` 预览与渲染成 MP4。

> 详细风格规范见 `reference/STYLE.md`;组件(场景)目录见 `reference/scene-catalog.md`;
> 可直接套用的模板见 `templates/composition.html`;完整示例见 `examples/sales-10-pitfalls.html`。

## 何时用本技能
当用户想要上述风格的竖版口播视频(典型:小红书/抖音知识口播、复盘清单类),且愿意用 HyperFrames(命令行 + AI 编码代理)生产时。

## 前置条件
- Node.js ≥ 22、已安装 ffmpeg、可运行 `npx hyperframes`(见 HyperFrames 文档 Quickstart)。
- 一段**出镜口播视频** → 放到合成同级的 `assets/bg.mp4`(竖版 1080×1920 最佳;横版会被裁切)。
- 一份**脚本**:逐句的中文 + 英文译文 + 每句要高亮的关键词;章节划分;需要的引用/产品卡片素材。

## 标准工作流(照此执行)
1. **拆解输入**。把脚本整理成一个"字幕表":每条 = {开始秒, 结束秒, 中文, 英文, 高亮词[]}。再列出章节(名称 + 各自时间区间)与卡片(来源标签、pill、图片/引文/品牌)。
   - 时间对齐:以 `assets/bg.mp4` 的真实语音为准。可先用工具/听写得到每句的起止秒数;没有就按语速估算,后续在 preview 里微调。
2. **复制模板**。把 `templates/composition.html` 复制为你的合成(如 `compositions/index.html`)。模板是一个 12 秒 demo,已含全部 4 个组件。
3. **填字幕**。按字幕表增删 `.cue` 块:中文放 `.zh`、英文放 `.en`,关键词用 `<span class="hl">…</span>` 包起来(中英都包,视觉一致)。给每个 cue 一个唯一 id。
4. **接时间轴**。在底部 `<script>` 的 GSAP 时间轴里,为每个 cue 加一对动画:进场 `fromTo(opacity0→1, y:30→0)` 放在该句开始秒,出场 `to(opacity→0)` 放在结束前 ~0.3s。卡片同理(用 scale+opacity)。
5. **配章节条**。按 `reference/scene-catalog.md` 的"章节进度条"小节,设置 done/active 类与各章 `.fill` 的填充动画(在各自时间窗内 scaleX:0→1)。
6. **对齐总时长**(最易错):把时间轴末尾 `tl.set({}, {}, TOTAL)` 的 `TOTAL` 改成视频真实总秒数,并把背景 `<video data-duration="TOTAL">`、容器 `.overlays data-duration="TOTAL"` 一并改成同值。否则视频会被提前截断。
7. **预览**:`npx hyperframes preview`,在浏览器里逐帧检查字幕同步、卡片进出、章节进度。改 HTML 即时热更新。
8. **渲染**:`npx hyperframes render --output out.mp4`。

## 必须遵守的技术约定(HyperFrames 的坑)
- GSAP 时间轴必须 `gsap.timeline({ paused: true })`,由框架按帧 seek 播放;**不要**自己 `tl.play()`。
- 注册到 `window.__timelines["root"]`,key 必须等于根元素 `data-composition-id`。
- **用 `fromTo` 或 `set` 设定初始态**,不要只用 `from`——渲染是按帧 seek 的,只用 `from` 在跳帧时会出现"该隐藏的元素却显示"的错。模板里 `.cue/.card` 初始 `opacity:0` 也是为此。
- **合成总时长 = 时间轴时长**。结尾用 `tl.set({}, {}, TOTAL)` 补足,且与背景视频 `data-duration` 一致。
- **不要在脚本里控制媒体**(`.play()`、设 `currentTime`),媒体播放由框架接管。
- **不要直接动画 `<video>` 的宽高/位置**,需要时包一层 `<div>` 动画外层。
- 用绝对定位排版;1080×1920 画布;字幕在下三分之一,章节条在顶部,卡片在上半部。

## 设计规则速记(细节见 STYLE.md)
- 高亮色仅一种:橙 `#EC6A35`,只给"关键词";其余中文纯白、英文用灰 `#C7C4BD`。
- 中文用 `Noto Sans SC` 900 加粗大字;英文/标签/卡片引文用等宽 `JetBrains Mono`,带字距。
- 卡片:暗色半透明 `rgba(22,20,18,.86)`、圆角 ~26px、头部 = 橙点 + 等宽来源标签 + 右上 pill。
- 动画克制:进出只用 透明度 + 轻微位移/缩放,时长 .3–.45s;章节进度条匀速 `ease:'none'`。
- 一屏同时最多一个"主角"(字幕 或 卡片 或 大标题),避免信息打架。
