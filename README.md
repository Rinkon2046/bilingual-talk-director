# bilingual-talk-director

**双语口播导演** 是一个面向 Codex / AI 编码代理的 HyperFrames 技能包，用来把竖屏出镜口播视频包装成更适合小红书、抖音、短视频课程和产品讲解的成片风格。

它会把一段普通口播素材整理成：

- 顶部章节进度条
- 底部中英双语强字幕
- 中文大字 + 英文等宽译文
- 关键词橙色高亮
- 暗色圆角引用卡片 / 产品卡片
- 可选的段落转场大标题

适合做知识口播、工具演示、产品复盘、观点拆解、销售/增长/AI 主题短视频，以及任何需要“真人讲解 + 信息层包装”的竖屏内容。

## What It Builds

这个技能生成的是 **HyperFrames HTML composition**，再通过 `npx hyperframes preview/render` 预览和渲染为 MP4。

典型输入：

- `assets/bg.mp4`：竖屏出镜口播视频，推荐 1080×1920
- 逐句脚本：开始时间、结束时间、中文、英文、关键词
- 章节：例如开场、问题、方法、案例、总结
- 卡片素材：引用来源、产品名、截图、CTA 或补充说明

典型输出：

- `compositions/index.html`
- 可预览的 HyperFrames 合成
- 最终短视频 `out.mp4`

## Visual Style

核心视觉来自“车内/桌前口播 + 强字幕信息层”的短视频包装方式：

- 背景视频满屏裁切
- 顶部轻量章节条常驻
- 底部使用黑色渐变托住字幕
- 中文字幕使用超粗大字
- 英文译文使用等宽小字
- 关键词只用一种橙色高亮：`#EC6A35`
- 卡片使用暗色半透明背景、圆角、来源标签和 pill 标记

详细规范见 [reference/STYLE.md](reference/STYLE.md)，组件片段见 [reference/scene-catalog.md](reference/scene-catalog.md)。

## Quick Start

把本目录放进 HyperFrames 项目的 `skills/` 目录，或按你使用的代理/技能管理方式安装。

然后对 Codex 说：

```text
使用 bilingual-talk-director 技能。
背景视频在 assets/bg.mp4。
请根据我的脚本生成 compositions/index.html，
加顶部章节进度条、底部中英双语字幕、关键词高亮和必要的暗色卡片。
先 preview，再 render 成 out.mp4。
```

脚本格式建议：

```text
0 | 4 | 天天把 AI 塞进别人的业务里 | shoving AI into other people's business | AI
4 | 8 | 公司的本质其实是销售 | the essence of a company is selling | 销售;selling
```

## Directory

```text
bilingual-talk-director/
├── SKILL.md
├── README.md
├── README_使用说明.md
├── reference/
│   ├── STYLE.md
│   └── scene-catalog.md
├── templates/
│   └── composition.html
└── examples/
    └── sales-10-pitfalls.html
```

## Notes

- 模板默认背景视频路径是 `../assets/bg.mp4`。
- 合成总时长必须与背景视频 `data-duration` 一致。
- GSAP 时间轴必须保持 `paused: true`，由 HyperFrames 按帧 seek。
- 字幕和卡片请使用 `fromTo` / `set` 设定初始态，避免跳帧渲染时显隐错误。

更多细节见 [README_使用说明.md](README_使用说明.md)。
