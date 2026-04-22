---
name: misc-ui-style
description: React/Vue Web 页面布局与样式规范（框架无关）。涵盖页面结构、视图布局 Pattern、CSS 变量 token 用法、卡片/面板规范、间距原则、状态色方案、数据表格、按钮系统。触发词：布局、样式、UI、卡片、颜色、主题、对比度、呼吸感、视图、页面、表格、按钮、Vue、React、CSS变量。
---

# 页面布局与样式规范

## 核心原则

1. 所有颜色通过 CSS 变量获取，**禁止硬编码颜色值**
2. 布局模式可组合复用，按场景选择
3. 间距遵循呼吸感原则，避免拥挤

## 禁止事项

- 禁止硬编码颜色值（如 `#0f3460`、`#16213e`）
- 禁止在组件内直接写死颜色，必须用 CSS 变量
- 新增颜色语义时，先在 `theme.css` 的 `:root` 和 `[data-theme="dark"]` 中同时添加变量
- 不要为 `label` 级别文字强求 7:1 对比度

---

## 速查卡

**Token 速查**

| Token | 用途 |
|-------|------|
| `c.bg` | 页面背景 |
| `c.surface` | 主要卡片面 |
| `c.panel` | 侧边栏/面板背景 |
| `c.border` | 边框/分割线 |
| `c.text` | 主要正文 |
| `c.textSub` | 次要正文 |
| `c.muted` | 辅助说明文字 |
| `c.label` | 时间戳等非关键文字 |
| `c.primary` | 主色（按钮/选中态） |
| `c.primaryHover` | 主色悬停 |
| `c.accent` | 强调色 |
| `c.cardBg` | 数据卡片背景 |
| `c.inputBg` | 输入框背景 |
| `c.rowHover` | 表格行悬停背景 |
| `c.green / c.red / c.yellow / c.purple` | 状态色 |

```tsx
import { useTheme } from './theme'
const { tokens: c } = useTheme()
```

**间距速查**

| 位置 | 值 |
|------|----|
| 卡片之间 gap | `10–12px` |
| 数据卡 padding | `14px 20px` |
| 图表卡 padding | `14px 16px` |
| 表单块 padding | `20px 24px` |
| 页面主体 padding | `16px` |
| 标签 → 数值 marginBottom | `6px` |
| 区块标题 marginBottom | `8px` |
| 表单区块 marginBottom | `16px` |
| 页头 marginBottom | `24px` |

---

## 整体页面结构

```
App (flex column, minHeight: 100vh)
├── TopNav (height: 52px, position: sticky, top: 0, z-index: 100)
└── main (flex: 1)
    └── <当前 View>
```

**TopNav**：`display: flex; alignItems: stretch; height: 52px`
- 左：Logo 区（带右边框）
- 中：导航项（`flex: 1`），活跃项用底部边框标识
- 右：主题切换按钮

---

## 布局 Pattern

### Pattern A：左右分栏（列表 + 详情）

用于列表页、详情页、数据管理面板。

| 参数 | 值 |
|------|----|
| 容器高度 | `calc(100vh - 52px)`, `overflow: hidden` |
| 左侧宽度 | `26%`, `minWidth: 320`, `maxWidth: 400` |
| 左侧右边框 | `1px solid c.border` |
| 列表项 padding | `12px 16px` |
| 选中态 | `borderLeft: 3px solid c.primary`, `background: rgba(99,102,241,0.15)` |
| 悬停态 | `background: c.rowHover` |
| 右侧 | `flex: 1`, `overflow: hidden`, `minWidth: 0` |

空状态（右侧无选中时）：
```tsx
<div style={{ height: '100%', display: 'flex', flexDirection: 'column',
  alignItems: 'center', justifyContent: 'center', gap: 12 }}>
  <div style={{ color: c.border, fontSize: 40 }}>←</div>
  <div style={{ color: c.muted, fontSize: 14 }}>选择左侧记录查看详情</div>
</div>
```

### Pattern B：全屏纵向（工具条 + 滚动主体）

用于仪表盘、详情嵌套、全屏进度视图。

| 参数 | 值 |
|------|----|
| 工具条 padding | `8px 16px` |
| 工具条底边框 | `1px solid c.border` |
| 主体 padding | `16px` |
| 主体 gap | `12px` |
| 2 列图表 | `gridTemplateColumns: 1fr 1fr`, `gap: 12px` |

### Pattern C：单列表单（居中，限宽）

用于表单页、配置页、创建/编辑视图。

| 参数 | 值 |
|------|----|
| 最大宽度 | `maxWidth: 800` |
| 外边距 | `padding: 32px 40px` |
| 页头 marginBottom | `24px` |
| 页头标题 | `fontSize: 20, fontWeight: 700, color: c.text` |
| 页头副标题 | `fontSize: 12, color: c.muted` |

常用内部网格：

| 场景 | 布局值 |
|------|---------------------|
| 3 列数字参数 | `1fr 1fr 1fr` |
| 2 列字段 | `1fr 1fr` |
| 主字段 + 操作按钮 + 次字段（flex 行） | `flex: 1.5` + `flexShrink: 0` + `flex: 1` |

---

## 卡片规范

```tsx
// MetricCard（数据指标卡）
{ border: `1px solid ${c.border}`, borderRadius: 10, padding: '14px 20px',
  textAlign: 'center', minWidth: 96 }
// 标签：color: c.muted, fontSize: 11, fontWeight: 600, textTransform: 'uppercase', letterSpacing: '0.07em', marginBottom: 6
// 数值：color: c.text, fontSize: 24, fontWeight: 700, letterSpacing: '-0.02em', lineHeight: 1

// ChartCard（图表容器卡）
{ border: `1px solid ${c.border}`, borderRadius: 10, padding: '14px 16px' }
// 标题：color: c.muted, fontSize: 11, fontWeight: 600, textTransform: 'uppercase', letterSpacing: '0.07em', marginBottom: 8

// FormBlock（表单区块）
{ border: `1px solid ${c.border}`, borderRadius: 12, padding: '20px 24px', marginBottom: 16 }
```

---

## 表单元素

```tsx
const inp: React.CSSProperties = {
  background: c.inputBg,
  border: `1px solid ${c.border}`,
  borderRadius: 8,
  height: 44,
  padding: '0 12px',
  color: c.text,
  fontSize: 14,
  width: '100%',
  boxSizing: 'border-box',
  outline: 'none',
}

const lbl: React.CSSProperties = {
  color: c.muted,
  fontSize: 11,
  fontWeight: 600,
  textTransform: 'uppercase',
  letterSpacing: '0.08em',
  marginBottom: 6,
  display: 'block',
}
```

---

## 状态色徽章

状态色使用 rgba 半透明背景 + token 文字色，亮暗两套主题下通用。

| 状态 | background | color | border |
|------|-----------|-------|--------|
| 成功/运行中 | `rgba(74,222,128,0.12)` | `c.green` | `rgba(74,222,128,0.3)` |
| 主色/已完成 | `rgba(99,102,241,0.12)` | `c.primary` | `rgba(99,102,241,0.3)` |
| 错误/已中止 | `rgba(248,113,113,0.12)` | `c.red` | `rgba(248,113,113,0.3)` |
| 警告 | `rgba(251,191,36,0.1)` | `c.yellow` | `rgba(251,191,36,0.3)` |
| 紫色标签 | `rgba(167,139,250,0.1)` | `c.purple` | `rgba(167,139,250,0.25)` |

徽章通用样式：`fontSize: 11, fontWeight: 600, padding: '2px 8px', borderRadius: 4, border: '1px solid'`

---

## 数据表格

```tsx
// 列头
const thStyle: React.CSSProperties = {
  background: c.surface,
  color: c.muted, fontSize: 11, fontWeight: 600,
  textTransform: 'uppercase', letterSpacing: '0.06em',
  padding: '8px 12px', borderBottom: `1px solid ${c.border}`,
  textAlign: 'left',
}

// 数据行单元格
const tdStyle: React.CSSProperties = {
  padding: '10px 12px',
  borderBottom: `1px solid ${c.border}`,
  color: c.text, fontSize: 13,
}
```

| 状态 | 样式 |
|------|------|
| 行悬停 | `background: c.rowHover` |
| 行选中 | `borderLeft: 3px solid c.primary`, `background: rgba(99,102,241,0.1)` |
| 操作列 | 右对齐，icon 按钮默认 `opacity: 0`，行 hover 时 `opacity: 1` |
| 空状态 | 居中，`color: c.muted`, `fontSize: 14` |
| 分页条 | `padding: 12px 16px`, `borderTop: 1px solid c.border`，flex 两端对齐 |

---

## 按钮系统

**变体**

| 变体 | background | color | border |
|------|-----------|-------|--------|
| primary | `c.primary` | `#fff` | none |
| secondary | `c.surface` | `c.text` | `1px solid c.border` |
| ghost | `transparent` | `c.muted` | none |
| danger | `rgba(248,113,113,0.12)` | `c.red` | `1px solid rgba(248,113,113,0.3)` |

**尺寸**

| 尺寸 | height | padding | fontSize |
|------|--------|---------|----------|
| sm | 28px | `0 10px` | 12px |
| md | 36px | `0 14px` | 13px |
| lg | 44px | `0 20px` | 14px |

通用样式：`borderRadius: 8, fontWeight: 600, cursor: pointer, transition: opacity 0.15s`

禁用态：`opacity: 0.4, cursor: not-allowed`

Hover 态：primary/danger 用 `opacity: 0.85`；secondary/ghost 用 `background: c.rowHover`

---

## 对比度要求（WCAG AA ≥ 4.5:1 / AAA ≥ 7:1）

| 级别 | Token | 说明 |
|------|-------|------|
| AAA ≥ 7:1 | `text` | 主要正文必须满足 |
| AAA ≥ 7:1 | `textSub` | 次要正文 |
| AA ≥ 4.5:1 | `muted` | 标签、辅助说明 |
| 推荐 | `label` | 时间戳等非关键文字 |

亮暗两套主题的 token 值需分别测试对比度。
