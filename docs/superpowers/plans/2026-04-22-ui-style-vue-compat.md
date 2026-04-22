# ui-style Skill Vue Compat Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 `misc/skills/ui-style/SKILL.md` 从 React 专用改为框架无关版本，使用 CSS 变量替代 `useTheme()` hook，同时适用于 React 和 Vue 项目。

**Architecture:** 单文件原地修改。将所有 `c.xxx` token 引用替换为 `var(--color-xxx)` CSS 变量，React 专用代码示例改为纯 CSS class 写法，新增"主题定义"章节提供完整的 `:root` / `[data-theme="dark"]` 变量模板。半透明主色使用 `color-mix()` 动态跟随 `--color-primary`。

**Tech Stack:** Markdown，CSS

---

## 文件

- Modify: `misc/skills/ui-style/SKILL.md`

---

### Task 1: 更新 frontmatter、核心原则、禁止事项

**Files:**
- Modify: `misc/skills/ui-style/SKILL.md`（第 1–19 行）

- [ ] **Step 1: 替换 frontmatter description**

将第 1–4 行替换为：

```markdown
---
name: misc-ui-style
description: React/Vue Web 页面布局与样式规范（框架无关）。涵盖页面结构、视图布局 Pattern、CSS 变量 token 用法、卡片/面板规范、间距原则、状态色方案、数据表格、按钮系统。触发词：布局、样式、UI、卡片、颜色、主题、对比度、呼吸感、视图、页面、表格、按钮、Vue、React、CSS变量。
---
```

- [ ] **Step 2: 更新核心原则第 1 条**

将：
```
1. 所有颜色通过 theme provider 获取，**禁止硬编码颜色值**
```
替换为：
```
1. 所有颜色通过 CSS 变量获取，**禁止硬编码颜色值**
```

- [ ] **Step 3: 更新禁止事项**

将禁止事项部分替换为：

```markdown
## 禁止事项

- 禁止硬编码颜色值（如 `#0f3460`、`#16213e`）
- 禁止在组件内直接写死颜色，必须用 CSS 变量
- 新增颜色语义时，先在 `theme.css` 的 `:root` 和 `[data-theme="dark"]` 中同时添加变量
- 不要为 `label` 级别文字强求 7:1 对比度
```

- [ ] **Step 4: 验证**

```bash
head -20 misc/skills/ui-style/SKILL.md
```

预期：前 20 行内出现"CSS 变量"和"禁止硬编码颜色值"。

- [ ] **Step 5: 提交**

```bash
git add misc/skills/ui-style/SKILL.md
git commit -m "feat(ui-style): update frontmatter and principles for framework-agnostic CSS vars"
```

---

### Task 2: 更新速查卡 Token 表，新增主题定义章节

**Files:**
- Modify: `misc/skills/ui-style/SKILL.md`（速查卡部分 + 新增章节）

- [ ] **Step 1: 替换速查卡 Token 表**

将"速查卡"章节的 Token 速查表和代码片段替换为：

```markdown
## 速查卡

**Token 速查**

| CSS 变量 | 用途 |
|---------|------|
| `var(--color-bg)` | 页面背景 |
| `var(--color-surface)` | 主要卡片面 |
| `var(--color-panel)` | 侧边栏/面板背景 |
| `var(--color-border)` | 边框/分割线 |
| `var(--color-text)` | 主要正文 |
| `var(--color-text-sub)` | 次要正文 |
| `var(--color-muted)` | 辅助说明文字 |
| `var(--color-label)` | 时间戳等非关键文字 |
| `var(--color-primary)` | 主色（按钮/选中态） |
| `var(--color-primary-hover)` | 主色悬停 |
| `var(--color-accent)` | 强调色 |
| `var(--color-card-bg)` | 数据卡片背景 |
| `var(--color-input-bg)` | 输入框背景 |
| `var(--color-row-hover)` | 表格行悬停背景 |
| `var(--color-green)` / `var(--color-red)` / `var(--color-yellow)` / `var(--color-purple)` | 状态色 |

```css
/* 使用方式：直接在 CSS / style 属性中引用变量 */
color: var(--color-text);
background: var(--color-bg);
border: 1px solid var(--color-border);
```
```

- [ ] **Step 2: 在速查卡之后、整体页面结构之前插入主题定义章节**

```markdown
---

## 主题定义

在项目根 CSS 文件（如 `theme.css`）中定义变量，亮暗两套：

```css
:root {
  --color-bg: #ffffff;
  --color-surface: #f8f8f8;
  --color-panel: #f0f0f0;
  --color-border: #e0e0e0;
  --color-text: #1a1a1a;
  --color-text-sub: #4a4a4a;
  --color-muted: #888888;
  --color-label: #aaaaaa;
  --color-primary: #6366f1;
  --color-primary-hover: #4f46e5;
  --color-accent: #f59e0b;
  --color-card-bg: #ffffff;
  --color-input-bg: #f5f5f5;
  --color-row-hover: rgba(0, 0, 0, 0.04);
  --color-green: #4ade80;
  --color-red: #f87171;
  --color-yellow: #fbbf24;
  --color-purple: #a78bfa;
}

[data-theme="dark"] {
  --color-bg: #0f0f0f;
  --color-surface: #1a1a1a;
  --color-panel: #141414;
  --color-border: #2a2a2a;
  --color-text: #e5e5e5;
  --color-text-sub: #b0b0b0;
  --color-muted: #707070;
  --color-label: #505050;
  --color-primary: #6366f1;
  --color-primary-hover: #818cf8;
  --color-accent: #f59e0b;
  --color-card-bg: #1a1a1a;
  --color-input-bg: #222222;
  --color-row-hover: rgba(255, 255, 255, 0.05);
  --color-green: #4ade80;
  --color-red: #f87171;
  --color-yellow: #fbbf24;
  --color-purple: #a78bfa;
}
```

切换主题：`document.documentElement.dataset.theme = 'dark'`
```

- [ ] **Step 3: 提交**

```bash
git add misc/skills/ui-style/SKILL.md
git commit -m "feat(ui-style): replace token table with CSS vars, add theme definition section"
```

---

### Task 3: 更新布局 Pattern（token 替换 + color-mix + 空状态）

**Files:**
- Modify: `misc/skills/ui-style/SKILL.md`（布局 Pattern 章节）

- [ ] **Step 1: 更新 Pattern A 参数表**

将 Pattern A 参数表替换为：

```markdown
| 参数 | 值 |
|------|----|
| 容器高度 | `calc(100vh - 52px)`, `overflow: hidden` |
| 左侧宽度 | `26%`, `min-width: 320px`, `max-width: 400px` |
| 左侧右边框 | `1px solid var(--color-border)` |
| 列表项 padding | `12px 16px` |
| 选中态 | `border-left: 3px solid var(--color-primary)`, `background: color-mix(in srgb, var(--color-primary) 15%, transparent)` |
| 悬停态 | `background: var(--color-row-hover)` |
| 右侧 | `flex: 1`, `overflow: hidden`, `min-width: 0` |
```

- [ ] **Step 2: 更新 Pattern A 空状态代码片段**

将空状态 JSX 替换为框架无关的 HTML + CSS 写法：

```markdown
空状态（右侧无选中时）：
```html
<div class="empty-state">
  <span style="color: var(--color-border); font-size: 40px">←</span>
  <p style="color: var(--color-muted); font-size: 14px">选择左侧记录查看详情</p>
</div>
```

```css
.empty-state {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
}
```
```

- [ ] **Step 3: 更新 Pattern B 参数表**

将 Pattern B 参数表替换为：

```markdown
| 参数 | 值 |
|------|----|
| 工具条 padding | `8px 16px` |
| 工具条底边框 | `1px solid var(--color-border)` |
| 主体 padding | `16px` |
| 主体 gap | `12px` |
| 2 列图表 | `grid-template-columns: 1fr 1fr`, `gap: 12px` |
```

- [ ] **Step 4: 更新 Pattern C 参数表**

将 Pattern C 参数表替换为：

```markdown
| 参数 | 值 |
|------|----|
| 最大宽度 | `max-width: 800px` |
| 外边距 | `padding: 32px 40px` |
| 页头 margin-bottom | `24px` |
| 页头标题 | `font-size: 20px, font-weight: 700, color: var(--color-text)` |
| 页头副标题 | `font-size: 12px, color: var(--color-muted)` |
```

- [ ] **Step 5: 提交**

```bash
git add misc/skills/ui-style/SKILL.md
git commit -m "feat(ui-style): update layout patterns to CSS vars and color-mix"
```

---

### Task 4: 更新卡片规范和表单元素

**Files:**
- Modify: `misc/skills/ui-style/SKILL.md`（卡片规范、表单元素章节）

- [ ] **Step 1: 替换卡片规范代码块**

将卡片规范的 TSX 样式对象替换为 CSS class 写法：

```markdown
## 卡片规范

```css
/* MetricCard（数据指标卡） */
.metric-card {
  border: 1px solid var(--color-border);
  border-radius: 10px;
  padding: 14px 20px;
  text-align: center;
  min-width: 96px;
}
.metric-card__label {
  color: var(--color-muted);
  font-size: 11px; font-weight: 600;
  text-transform: uppercase; letter-spacing: 0.07em;
  margin-bottom: 6px;
}
.metric-card__value {
  color: var(--color-text);
  font-size: 24px; font-weight: 700;
  letter-spacing: -0.02em; line-height: 1;
}

/* ChartCard（图表容器卡） */
.chart-card {
  border: 1px solid var(--color-border);
  border-radius: 10px;
  padding: 14px 16px;
}
.chart-card__title {
  color: var(--color-muted);
  font-size: 11px; font-weight: 600;
  text-transform: uppercase; letter-spacing: 0.07em;
  margin-bottom: 8px;
}

/* FormBlock（表单区块） */
.form-block {
  border: 1px solid var(--color-border);
  border-radius: 12px;
  padding: 20px 24px;
  margin-bottom: 16px;
}
```
```

- [ ] **Step 2: 替换表单元素代码块**

将表单元素的 `React.CSSProperties` 对象替换为 CSS class 写法：

```markdown
## 表单元素

```css
.inp {
  background: var(--color-input-bg);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  height: 44px;
  padding: 0 12px;
  color: var(--color-text);
  font-size: 14px;
  width: 100%;
  box-sizing: border-box;
  outline: none;
}

.lbl {
  color: var(--color-muted);
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 6px;
  display: block;
}
```
```

- [ ] **Step 3: 提交**

```bash
git add misc/skills/ui-style/SKILL.md
git commit -m "feat(ui-style): convert card and form styles to CSS classes"
```

---

### Task 5: 更新状态色徽章、数据表格、按钮系统、对比度要求

**Files:**
- Modify: `misc/skills/ui-style/SKILL.md`（后半部分章节）

- [ ] **Step 1: 替换状态色徽章表格**

将徽章表格替换为：

```markdown
## 状态色徽章

状态色使用 rgba 半透明背景 + CSS 变量文字色，亮暗两套主题下通用。

| 状态 | background | color | border |
|------|-----------|-------|--------|
| 成功/运行中 | `rgba(74,222,128,0.12)` | `var(--color-green)` | `rgba(74,222,128,0.3)` |
| 主色/已完成 | `rgba(99,102,241,0.12)` | `var(--color-primary)` | `rgba(99,102,241,0.3)` |
| 错误/已中止 | `rgba(248,113,113,0.12)` | `var(--color-red)` | `rgba(248,113,113,0.3)` |
| 警告 | `rgba(251,191,36,0.1)` | `var(--color-yellow)` | `rgba(251,191,36,0.3)` |
| 紫色标签 | `rgba(167,139,250,0.1)` | `var(--color-purple)` | `rgba(167,139,250,0.25)` |

徽章通用样式：`font-size: 11px, font-weight: 600, padding: 2px 8px, border-radius: 4px, border: 1px solid`
```

- [ ] **Step 2: 替换数据表格代码块和状态表**

将数据表格章节替换为：

```markdown
## 数据表格

```css
/* 列头 */
.th {
  background: var(--color-surface);
  color: var(--color-muted); font-size: 11px; font-weight: 600;
  text-transform: uppercase; letter-spacing: 0.06em;
  padding: 8px 12px; border-bottom: 1px solid var(--color-border);
  text-align: left;
}

/* 数据行单元格 */
.td {
  padding: 10px 12px;
  border-bottom: 1px solid var(--color-border);
  color: var(--color-text); font-size: 13px;
}
```

| 状态 | 样式 |
|------|------|
| 行悬停 | `background: var(--color-row-hover)` |
| 行选中 | `border-left: 3px solid var(--color-primary)`, `background: color-mix(in srgb, var(--color-primary) 10%, transparent)` |
| 操作列 | 右对齐，icon 按钮默认 `opacity: 0`，行 hover 时 `opacity: 1` |
| 空状态 | 居中，`color: var(--color-muted)`, `font-size: 14px` |
| 分页条 | `padding: 12px 16px`, `border-top: 1px solid var(--color-border)`，flex 两端对齐 |
```

- [ ] **Step 3: 替换按钮系统变体表格**

将按钮系统变体表格替换为：

```markdown
**变体**

| 变体 | background | color | border |
|------|-----------|-------|--------|
| primary | `var(--color-primary)` | `#fff` | none |
| secondary | `var(--color-surface)` | `var(--color-text)` | `1px solid var(--color-border)` |
| ghost | `transparent` | `var(--color-muted)` | none |
| danger | `rgba(248,113,113,0.12)` | `var(--color-red)` | `1px solid rgba(248,113,113,0.3)` |
```

将 Hover 态说明替换为：

```markdown
Hover 态：primary/danger 用 `opacity: 0.85`；secondary/ghost 用 `background: var(--color-row-hover)`
```

- [ ] **Step 4: 替换对比度要求表格**

将对比度表格替换为：

```markdown
## 对比度要求（WCAG AA ≥ 4.5:1 / AAA ≥ 7:1）

| 级别 | CSS 变量 | 说明 |
|------|---------|------|
| AAA ≥ 7:1 | `--color-text` | 主要正文必须满足 |
| AAA ≥ 7:1 | `--color-text-sub` | 次要正文 |
| AA ≥ 4.5:1 | `--color-muted` | 标签、辅助说明 |
| 推荐 | `--color-label` | 时间戳等非关键文字 |

亮暗两套主题的变量值需分别测试对比度。
```

- [ ] **Step 5: 验证文件行数**

```bash
wc -l misc/skills/ui-style/SKILL.md
```

预期：280–330 行之间。

- [ ] **Step 6: 验证无残留 React 专用内容**

```bash
grep -n "useTheme\|React\.CSSProperties\|tokens: c\b" misc/skills/ui-style/SKILL.md
```

预期：无输出（0 匹配）。

- [ ] **Step 7: 验证 CSS 变量引用一致**

```bash
grep -n "c\.\(bg\|text\|border\|primary\|muted\|surface\|panel\|rowHover\|inputBg\|cardBg\)" misc/skills/ui-style/SKILL.md
```

预期：无输出（所有旧 token 引用已替换）。

- [ ] **Step 8: 提交**

```bash
git add misc/skills/ui-style/SKILL.md
git commit -m "feat(ui-style): update badges, table, button, contrast to CSS vars"
```
