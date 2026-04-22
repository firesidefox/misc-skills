# ui-style 技能 Vue 适配（框架无关化）设计规范

**日期**：2026-04-22  
**目标**：将 `misc/skills/ui-style/SKILL.md` 从 React 专用改为框架无关版本，同时适用于 React 和 Vue 项目

---

## 问题

现有技能使用 React 专用 API：
- `React.CSSProperties` 类型
- `useTheme()` hook + `const { tokens: c } = useTheme()` 模式
- JSX 内联样式语法（`style={{ color: c.text }}`）
- token 引用方式 `c.xxx`

Vue 项目无法直接使用这些示例。

---

## 方案

采用 **CSS 变量**作为 token 系统，替代 React 的 `useTheme()` hook。

**为什么选 CSS 变量：**
- 真正框架无关——React 和 Vue 都能直接使用
- 亮暗模式切换只需切换 `document.documentElement.dataset.theme`，无需 JS 运行时
- Vue `<style scoped>` 可直接使用，React 内联样式或 CSS 文件均可使用
- 主流设计系统（Tailwind、Material、Ant Design）的通行做法

**Token 命名规范：** `--color-xxx`（加 `color-` 前缀，避免与其他 CSS 变量冲突）

---

## 改动范围

文件：`misc/skills/ui-style/SKILL.md`（单文件原地修改）

### 1. frontmatter

description 去掉 React 专用描述，触发词加入 Vue：

```
description: React/Vue Web 页面布局与样式规范（框架无关）。涵盖页面结构、视图布局 Pattern、CSS 变量 token 用法、卡片/面板规范、间距原则、状态色方案、数据表格、按钮系统。触发词：布局、样式、UI、卡片、颜色、主题、对比度、呼吸感、视图、页面、表格、按钮、Vue、React、CSS变量。
```

### 2. 核心原则 + 禁止事项

措辞更新：

- "所有颜色通过 theme provider 获取" → "所有颜色通过 CSS 变量获取"
- 禁止事项中 "必须用 token" → "必须用 CSS 变量"
- 删除 "在 theme.tsx 的 dark/light 对象中同时添加 token" → 改为 "在 theme.css 的 `:root` 和 `[data-theme='dark']` 中同时添加变量"

### 3. 速查卡 Token 表

token 引用方式从 `c.xxx` 改为 `var(--color-xxx)`：

| Token | CSS 变量 | 用途 |
|-------|---------|------|
| `c.bg` | `var(--color-bg)` | 页面背景 |
| `c.surface` | `var(--color-surface)` | 主要卡片面 |
| `c.panel` | `var(--color-panel)` | 侧边栏/面板背景 |
| `c.border` | `var(--color-border)` | 边框/分割线 |
| `c.text` | `var(--color-text)` | 主要正文 |
| `c.textSub` | `var(--color-text-sub)` | 次要正文 |
| `c.muted` | `var(--color-muted)` | 辅助说明文字 |
| `c.label` | `var(--color-label)` | 时间戳等非关键文字 |
| `c.primary` | `var(--color-primary)` | 主色（按钮/选中态） |
| `c.primaryHover` | `var(--color-primary-hover)` | 主色悬停 |
| `c.accent` | `var(--color-accent)` | 强调色 |
| `c.cardBg` | `var(--color-card-bg)` | 数据卡片背景 |
| `c.inputBg` | `var(--color-input-bg)` | 输入框背景 |
| `c.rowHover` | `var(--color-row-hover)` | 表格行悬停背景 |
| `c.green/red/yellow/purple` | `var(--color-green)` 等 | 状态色 |

删除 `useTheme()` 代码片段，替换为：

```css
/* 使用方式：直接在 CSS / style 属性中引用变量 */
color: var(--color-text);
background: var(--color-bg);
border: 1px solid var(--color-border);
```

### 4. 新增"主题定义"章节

放在速查卡之后、整体页面结构之前。提供完整的 CSS 变量定义模板：

```css
/* theme.css */
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

### 5. 布局 Pattern

**token 引用全量替换**（`c.xxx` → `var(--color-xxx)`）

**半透明主色改用 `color-mix()`**（浏览器支持：Chrome 111+、Firefox 113+、Safari 16.2+）：

| 原来 | 改后 |
|------|------|
| `rgba(99,102,241,0.15)` | `color-mix(in srgb, var(--color-primary) 15%, transparent)` |
| `rgba(99,102,241,0.1)` | `color-mix(in srgb, var(--color-primary) 10%, transparent)` |

**空状态代码片段**从 JSX 改为框架无关的 HTML 写法：

```html
<!-- 空状态：height: 100%, flex column, 居中 -->
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

### 6. 卡片规范

从 TSX 样式对象改为 CSS class 写法：

```css
/* MetricCard */
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

/* ChartCard */
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

/* FormBlock */
.form-block {
  border: 1px solid var(--color-border);
  border-radius: 12px;
  padding: 20px 24px;
  margin-bottom: 16px;
}
```

### 7. 表单元素

从 `React.CSSProperties` 对象改为 CSS class：

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

### 8. 状态色徽章

token 名替换，`rgba` 值保持不变（状态色不需要 `color-mix()`，因为不跟随主色变化）：

| 状态 | background | color | border |
|------|-----------|-------|--------|
| 成功/运行中 | `rgba(74,222,128,0.12)` | `var(--color-green)` | `rgba(74,222,128,0.3)` |
| 主色/已完成 | `rgba(99,102,241,0.12)` | `var(--color-primary)` | `rgba(99,102,241,0.3)` |
| 错误/已中止 | `rgba(248,113,113,0.12)` | `var(--color-red)` | `rgba(248,113,113,0.3)` |
| 警告 | `rgba(251,191,36,0.1)` | `var(--color-yellow)` | `rgba(251,191,36,0.3)` |
| 紫色标签 | `rgba(167,139,250,0.1)` | `var(--color-purple)` | `rgba(167,139,250,0.25)` |

### 9. 数据表格

从 `React.CSSProperties` 对象改为 CSS class：

```css
.th {
  background: var(--color-surface);
  color: var(--color-muted);
  font-size: 11px; font-weight: 600;
  text-transform: uppercase; letter-spacing: 0.06em;
  padding: 8px 12px;
  border-bottom: 1px solid var(--color-border);
  text-align: left;
}

.td {
  padding: 10px 12px;
  border-bottom: 1px solid var(--color-border);
  color: var(--color-text);
  font-size: 13px;
}
```

行状态表格中 token 名替换：

| 状态 | 样式 |
|------|------|
| 行悬停 | `background: var(--color-row-hover)` |
| 行选中 | `border-left: 3px solid var(--color-primary)`, `background: color-mix(in srgb, var(--color-primary) 10%, transparent)` |
| 操作列 | 右对齐，icon 按钮默认 `opacity: 0`，行 hover 时 `opacity: 1` |
| 空状态 | 居中，`color: var(--color-muted)`, `font-size: 14px` |
| 分页条 | `padding: 12px 16px`, `border-top: 1px solid var(--color-border)`，flex 两端对齐 |

### 10. 按钮系统

token 名替换：

| 变体 | background | color | border |
|------|-----------|-------|--------|
| primary | `var(--color-primary)` | `#fff` | none |
| secondary | `var(--color-surface)` | `var(--color-text)` | `1px solid var(--color-border)` |
| ghost | `transparent` | `var(--color-muted)` | none |
| danger | `rgba(248,113,113,0.12)` | `var(--color-red)` | `1px solid rgba(248,113,113,0.3)` |

Hover 态：primary/danger 用 `opacity: 0.85`；secondary/ghost 用 `background: var(--color-row-hover)`

### 11. 对比度要求

token 名替换：

| 级别 | CSS 变量 | 说明 |
|------|---------|------|
| AAA ≥ 7:1 | `--color-text` | 主要正文必须满足 |
| AAA ≥ 7:1 | `--color-text-sub` | 次要正文 |
| AA ≥ 4.5:1 | `--color-muted` | 标签、辅助说明 |
| 推荐 | `--color-label` | 时间戳等非关键文字 |

---

## 预期效果

- 技能同时适用于 React 和 Vue 项目
- 新增"主题定义"章节，技能自包含（开发者知道如何配套定义变量）
- 半透明主色使用 `color-mix()` 动态跟随 `--color-primary`
- 文件行数预计增加约 40 行（主题定义章节），总计约 310 行
