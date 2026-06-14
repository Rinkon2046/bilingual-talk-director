# 组件目录 · scene-catalog.md

四个可复用组件(场景)。每个给出 HTML 结构 + 对应的 GSAP 片段。把它们组合进 `templates/composition.html` 即可。
所有时间都用"绝对秒"作为 GSAP 位置参数(第 3 个参数)。

---

## ① 章节进度条 chapter-bar(全程常驻)
顶部一排章节,当前章 `active` 标签高亮、进度随时间填充;已过的章 `done` 填满;未到的留空。

HTML(N 章按需增减):
```html
<div class="chapter-bar">
  <div class="chapter done"><span class="lab">开场</span><div class="track"><div class="fill"></div></div></div>
  <div class="chapter active"><span class="lab">坑 1-3</span><div class="track"><div class="fill"></div></div></div>
  <div class="chapter"><span class="lab">坑 4-6</span><div class="track"><div class="fill"></div></div></div>
  <!-- … -->
</div>
```

GSAP —— 让"高亮 + 进度"随视频推进逐章切换(把每章的时间窗填进去):
```js
// 约定:chapters = [{sel,'.c0'}, t0, t1] 各章起止秒
// 简化做法:每章一个 .fill 在自己时间窗内 scaleX 0→1;并在边界切换标签颜色
// 第1章 0–60s
tl.fromTo('.c0 .fill', {scaleX:0}, {scaleX:1, duration:60, ease:'none'}, 0);
tl.set('.c1 .lab', {color:'#fff', fontWeight:600}, 60);   // 进入第2章高亮
tl.set('.c0 .lab', {color:'#E9E6E0'}, 60);                 // 第1章转 done 态
tl.fromTo('.c1 .fill', {scaleX:0}, {scaleX:1, duration:55, ease:'none'}, 60);
// … 依此类推
```
> 给每个 `.chapter` 加一个类名(`.c0/.c1/…`)便于精确定位。初始把"未到"章的 `.fill` 保持 scaleX:0(CSS 已默认)。

---

## ② 双语字幕 cue(主力,逐句)
每句一个 `.cue`,中文 `.zh` + 英文 `.en`,关键词 `.hl`。

HTML:
```html
<div class="cue" id="cueN">
  <div class="zh">然后被<span class="hl">面子</span>和利益裹挟</div>
  <div class="en">getting hijacked by <span class="hl">face</span> and self-interest</div>
</div>
```

GSAP(start=该句开始秒, end=结束秒):
```js
tl.fromTo('#cueN', {opacity:0, y:30}, {opacity:1, y:0, duration:.35, ease:'power2.out'}, start);
tl.to('#cueN', {opacity:0, duration:.3}, end - 0.3);
```

---

## ③ 引用 / 产品卡片 card
暗色圆角卡,头部 = 橙点 + 来源标签 + pill;主体 = 图片 / 引文 / 产品卡。

HTML(引文型):
```html
<div class="card" id="cardN">
  <div class="card-head">
    <span class="dot"></span>
    <span class="src">Naval · Modern Wisdom</span>
    <span class="pill">原片</span>
  </div>
  <img class="card-media" src="../assets/quote.jpg" alt="" />
  <div class="card-quote">find what feels like play to you, then <span class="hl">productize yourself</span></div>
</div>
```

HTML(产品卡型 —— 内嵌白色"浏览器卡" + 品牌字 + tagline):
```html
<div class="card" id="cardProd">
  <div class="card-head"><span class="dot"></span><span class="src">hedj.io · Personal Career OS</span><span class="pill">我做的</span></div>
  <div style="background:#fff;border-radius:14px;padding:26px 28px;color:#1b1a17">
    <div style="font-weight:700;font-size:34px">How long until AI <mark style="background:#111;color:#fff;padding:0 6px;border-radius:4px">replaces</mark> you?</div>
    <div style="color:#666;font-size:20px;margin-top:10px">Paste your LinkedIn. Get a full career analysis…</div>
  </div>
  <div style="font-family:var(--font-zh);font-weight:900;color:var(--hl);font-size:64px;margin-top:18px">Hedj</div>
  <div style="color:#cfccc4;font-size:26px;margin-top:4px">帮你在 AI 时代,建立自己的职业护城河</div>
</div>
```

GSAP(在被提到的区间出现):
```js
tl.fromTo('#cardN', {opacity:0, scale:.96, y:22}, {opacity:1, scale:1, y:0, duration:.45, ease:'power2.out'}, start);
tl.to('#cardN', {opacity:0, scale:.985, duration:.35}, end - 0.35);
```

---

## ④ 强调大标题 bigtitle(转场金句,可选)
段落之间的"金句"全屏大字,通常不与字幕同时出现。

HTML:
```html
<div class="bigtitle" id="titleN">被<span class="hl">面子</span>和<br>利益裹挟</div>
```

GSAP:
```js
tl.fromTo('#titleN', {opacity:0, scale:.92}, {opacity:1, scale:1, duration:.5, ease:'power2.out'}, start);
tl.to('#titleN', {opacity:0, duration:.35}, end - 0.35);
```

---

## 组合建议
- 全程:章节条常驻 + 字幕逐句。
- 当口播提到"别人的观点/原片/自己的产品"时,叠 ③ 卡片 2–4 秒。
- 章节切换处或抛金句时,插 ④ 大标题 1.5–2.5 秒(此时可让字幕暂歇)。
- 收尾固定一张"产品卡 / CTA",定格 1–2 秒便于截图。
