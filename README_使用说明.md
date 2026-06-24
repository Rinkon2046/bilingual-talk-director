# 使用说明 · 双语口播导演 Skill(HyperFrames）

这是一个给 **HyperFrames(HeyGen 开源 / 写 HTML 渲染视频)** 用的技能包,
让你(或 Codex 这类编码代理)快速做出"车内口播 + 双语强字幕 + 章节进度条 + 暗色卡片"风格的竖版视频。

## 一、目录
```
bilingual-talk-director/
├── SKILL.md                     技能主文件(给 Codex 读的指令 + 触发说明)
├── README_使用说明.md           本文件
├── reference/
│   ├── STYLE.md                 精确风格规范(颜色/字体/尺寸/动效)
│   └── scene-catalog.md         4 个组件的 HTML + GSAP 片段
├── templates/
│   └── composition.html         可直接预览/渲染的 12 秒模板(含全部组件)
└── examples/
    └── sales-10-pitfalls.html   "销售10个坑"主题的小示例
```
> 注意:模板里背景视频指向 `../assets/bg.mp4`、卡片图指向 `../assets/quote.jpg`。
> 实际使用时在项目里建一个 `assets/` 放你的素材(出镜视频、卡片图)。

## 二、准备环境(一次性)
1. 安装 **Node.js ≥ 22** 和 **ffmpeg**。
2. 拉取/初始化一个 HyperFrames 项目(见 https://hyperframes.heygen.com/quickstart )。
   最简单:`GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/heygen-com/hyperframes.git`
   然后在仓库里安装依赖、可运行 `npx hyperframes`。

## 三、把本 Skill 装给 Codex
HyperFrames 自带各代理的插件目录(`.codex-plugin`、`.claude-plugin`、`.cursor-plugin`)与 `skills/`。
把本文件夹 `bilingual-talk-director/` 整个放进 HyperFrames 项目的 **`skills/`** 目录下即可被代理发现。
- 用 **Codex**:在该项目里启动 Codex,让它"使用 bilingual-talk-director 技能,基于我的脚本和 assets/bg.mp4 生成合成"。Codex 会读取 `SKILL.md` 并按其工作流执行。
- 也可用官方方式 `npx skills add …` 注册技能(以你所用版本的 HyperFrames 文档为准)。

## 四、用 Codex 制作一条视频(推荐流程)
把素材和脚本准备好,然后给 Codex 一句话指令,例如:

> 用 `bilingual-talk-director` 技能。背景视频在 `assets/bg.mp4`,共约 280 秒。
> 这是我的脚本(每行:开始秒 | 结束秒 | 中文 | 英文 | 高亮词):
> ```
> 0 | 4 | 天天把 AI 塞进别人的业务里 | shoving AI into others' business | AI
> 4 | 8 | 公司的本质其实是销售 | the essence of a company is selling | 销售;selling
> …
> ```
> 章节:开场(0–20)/ 坑1-3(20–120)/ 坑4-6(120–210)/ …
> 在提到 Naval 那段(t=95–99)叠引用卡片,图片用 `assets/naval.jpg`。
> 生成 `compositions/index.html`,先 `npx hyperframes preview` 让我看,再 `render` 成 `out.mp4`。

Codex 会:复制 `templates/composition.html` → 按脚本填 `.cue` 和卡片 → 接 GSAP 时间轴 →
把总时长补到 280s → 预览 → 渲染。

## 五、自己手动做(不依赖代理)
1. 复制 `templates/composition.html` 到 `compositions/index.html`。
2. 把 `assets/bg.mp4` 换成你的出镜视频;按 `reference/scene-catalog.md` 增删 `.cue`/卡片。
3. 字幕尽量保持单行:中文一行、英文一行。长句先拆成更短 cue;单行略超宽时缩小字号,不要默认换成两行。
4. 改 GSAP 时间轴里的进出时间;**把结尾 `tl.set({},{},12)` 的 12 改成真实总秒数**,
   并同步改 `<video data-duration>` 与 `.overlays data-duration`。
5. 预览:`npx hyperframes preview`(浏览器逐帧看,热更新)。
6. 渲染:`npx hyperframes render --output out.mp4`。

## 六、配音轨(可选)
默认用出镜视频原声。若要单独配音:
- 背景 `<video>` 加 `muted`,再加一条音频轨:
  ```html
  <audio src="../assets/voice.wav" data-start="0" data-duration="280" data-track-index="2"></audio>
  ```
- 字幕时间对齐到这条音频。

## 七、最常见的坑(务必看)
- **视频被提前截断** → 时间轴总时长 < 视频时长。结尾补 `tl.set({}, {}, 真实总秒数)`,并让 `data-duration` 一致。
- **该隐藏的字幕却一直显示** → 用了 `from` 而非 `fromTo/set`。所有 `.cue/.card` 必须有明确初始 `opacity:0`,进场用 `fromTo`。
- **中文不显示/字体不对** → 渲染机器要能加载 `Noto Sans SC`(联网或自托管字体);建议把字体文件放进 assets 并用 `@font-face` 自托管以保证可复现。
- **字幕换成两行/和口播节奏不贴** → cue 太长。按语音停顿重新切短,保持中文一行、英文一行;必要时动态降低字号。
- **卡片/视频闪烁** → 不要直接动画 `<video>` 尺寸;不要在脚本里 `.play()`。

## 八、字体
模板用 Google Fonts 的 `Noto Sans SC`(中文 900)+ `JetBrains Mono`(英文)。
追求渲染稳定可下载这两款字体到 `assets/fonts/` 并改成 `@font-face` 自托管。
