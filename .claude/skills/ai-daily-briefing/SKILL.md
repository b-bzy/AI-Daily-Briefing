---
name: ai-daily-briefing
description: 生成 AI 行业每日早报 Markdown 文件，使用 Telegram HTML 格式 + ---SPLIT--- 分条结构，覆盖语音模型 / AI for Sales / 综合三大板块。当用户要求"写今天的简报"、"出 AI 早报"、"补一份 briefing"等任务时使用。
---

# AI Daily Briefing Skill

为 `b-bzy/ai-daily-briefing` 仓库生成符合发布管线的早报文件。生成结果会被 `.github/workflows/daily-briefing.yml` 自动按 `---SPLIT---` 拆分后逐条发送到 Telegram。

## 输出位置与命名

- 路径：`briefings/YYYY-MM-DD-AM.md`（早报固定后缀 `-AM`）
- 时区：标题里的时间使用 **GST**（海湾标准时间，UTC+4）
- 一次任务只产出一个文件，不要修改历史简报

## 文件骨架（严格遵守）

```
<b>📰 AI 早报 · YYYY-MM-DD HH:MM GST</b>
<i>🎙️ 语音 N 条 | 💼 Sales N 条 | 🌐 综合 N 条</i>

---SPLIT---

<b>🎙️ 板块 A · 语音模型动态</b>

<新闻条目 1>
<新闻条目 2>
...

---SPLIT---

<b>💼 板块 B · AI for Sales 动态</b>

<新闻条目 ...>

---SPLIT---

<b>🌐 板块 C · AI 行业综合动态</b>

<新闻条目 ...>
```

规则：
- `---SPLIT---` 必须独占一行，前后各留一个空行
- 每个 `---SPLIT---` 之间的内容必须 < 4000 字符（workflow 单条 Telegram 上限 4096，留余量）
- 若某板块当日无显著进展，正文写：`🈚️ 过去 24 小时内无显著进展`
- 头部计数行的数字必须与正文条数一致

## 单条新闻模板

```
<b>序号️⃣ 新闻标题（一句话讲清楚发生了什么 + 关键数字/主体）</b>
<i>🕐 MM-DD · 距今约 N 小时</i>
<blockquote expandable><b>📝 发生了什么</b>
2-4 句完整叙事，覆盖主体 / 时间 / 动作 / 关键细节。

<b>📊 关键数据</b>
- 数据点 1（带单位/百分比/金额）
- 数据点 2
- 数据点 3
- 数据点 4（建议 3-5 条）

<b>💡 为什么重要</b>
1-2 句话讲清楚对行业 / 竞争格局 / 目标读者的意义。

<b>🔗 信息源</b>
<a href="https://...">阅读原文</a></blockquote>
```

序号使用 emoji 数字键：`1️⃣ 2️⃣ 3️⃣ 4️⃣ 5️⃣`。

更新型新闻在标题前加 `[更新]` 前缀。

## Telegram HTML 格式约束

只允许使用以下标签：`<b>`、`<i>`、`<a href="...">`、`<blockquote expandable>`、`<code>`、`<pre>`。

**必须转义 HTML 实体**：
- `&` → `&amp;`
- `<` → `&lt;`
- `>` → `&gt;`

特别注意公司名 / 产品名里的 `&`（如 `Q&amp;A`、`AT&amp;T`）。

**禁止**：
- Markdown 语法（`**bold**`、`# heading`、`- list` 之外的列表用 `-` 前缀但不要嵌套）
- `<br>`、`<p>`、`<div>` 等 Telegram 不支持的标签
- 表格、图片标签

## 内容选题规则

- **板块 A 语音模型**：TTS、ASR、Voice Agent、语音克隆、音乐生成、ElevenLabs / Suno / 小米 MiMo / Deepgram / Cartesia 等
- **板块 B AI for Sales**：AI SDR、销售自动化、CRM AI、Outreach / Salesloft / Clay / 11x / Regie 等
- **板块 C 综合**：模型发布、融资 / IPO、监管 / 政策、巨头动态、并购、诉讼

时间窗口：默认覆盖**过去 24 小时**。`🕐` 行的"距今约 N 小时"以早报标题里的时间为基准计算。

## 校验清单（提交前自检）

1. 标题日期、时间、时区正确
2. 计数行数字 = 实际条数
3. 三个 `---SPLIT---` 完整存在，且每段 < 4000 字符
4. 所有 `<blockquote expandable>` 都有对应的 `</blockquote>` 闭合
5. 所有 `<a href="...">阅读原文</a>` 链接可访问
6. 所有 `&` 已转义为 `&amp;`
7. 无 Markdown 残留（`**`、`##`、`---` 之类，除了分隔符自身）
8. 无空板块漏写 `🈚️` 占位

## 提交流程

1. 写入 `briefings/YYYY-MM-DD-AM.md`
2. `git add briefings/YYYY-MM-DD-AM.md`
3. commit message 风格：`Add briefing YYYY-MM-DD-AM`（参考历史 commit）
4. push 到 `main` 后 GitHub Actions 会自动触发 Telegram 发送

不要在同一个 commit 里混入其他文件改动，否则 workflow 的 `git diff` 选取逻辑可能选错文件。
