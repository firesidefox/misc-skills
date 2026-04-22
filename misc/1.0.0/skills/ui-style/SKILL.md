---
name: misc-ui-style
description: React Web 页面布局与样式规范。涵盖页面结构、视图布局 Pattern、主题 token 用法、卡片/面板规范、间距原则、状态色方案。触发词：布局、样式、UI、卡片、颜色、主题、对比度、呼吸感、视图、页面。
---

# 页面布局与样式规范

## 概述

本规范定义 React Web 应用的通用页面布局与样式原则，适用于：

- 使用 theme token 系统的主题化应用
- 需要支持亮/暗模式切换的项目
- 中后台类 Web 应用（列表页、详情页、表单页、仪表盘）

**核心原则**：

1. 所有颜色通过 theme provider 获取，禁止硬编码颜色值
2. 布局模式可组合复用，按场景选择
3. 间距遵循呼吸感原则，避免拥挤

---

## 目录

- [整体页面结构](#整体页面结构)
- [布局 Pattern](#布局-pattern)
- [卡片规范](#卡片规范)
- [间距原则](#间距原则)
- [主题系统](#主题系统)
- [状态色徽章模式](#状态色徽章模式)
- [表单元素样式](#表单元素样式)
- [对比度要求](#对比度要求)
- [禁止事项](#禁止事项)
- [快速参考卡](#快速参考卡)

---

## 整体页面结构

```
App (flex column, minHeight: 100vh)
├── TopNav (height: 52px, position: sticky, top: 0, z-index: 100)
└── main (flex: 1)
    └── <当前 View>
```

**TopNav** 布局：`display: flex; alignItems: stretch; height: 52px`
- 左：Logo 区（带右边框）
- 中：导航项（`flex: 1`），活跃项用底部边框标识
- 右：主题切换按钮

**视图切换**（App.tsx state）：

| state | 渲染组件 |
|-------|---------|
| `'list'` | ListView |
| `'detail'` | DetailView |
| `'form'` | FormView |
| `'dashboard'` | DashboardView |

---

## 布局 Pattern

### Pattern A：左右分栏（列表 + 详情）

用于列表页、详情页、数据管理面板。

```tsx
<div style={{
  display: 'flex',
  height: 'calc(100vh - 52px)',
  overflow: 'hidden',
}}>
  {/* 左侧列表面板 */}
  <div style={{
    width: '26%',
    minWidth: 320,
    maxWidth: 400,
    borderRight: `1px solid ${c.border}`,
    display: 'flex',
    flexDirection: 'column',
    flexShrink: 0,
  }}>
    {/* 工具栏 */}
    <div style={{ padding: '16px 16px 12px', borderBottom: `1px solid ${c.border}`,
      display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
      ...
    </div>
    {/* 列表区（可滚动） */}
    <div style={{ overflowY: 'auto', flex: 1 }}>
      {/* 列表项 */}
      <div style={{
        padding: '12px 16px',
        borderBottom: `1px solid ${c.border}`,
        borderLeft: selected ? '3px solid #6366f1' : '3px solid transparent',
        background: selected ? 'rgba(99,102,241,0.15)' : hovered ? c.rowHover : 'transparent',
        cursor: 'pointer',
      }}>
        ...
      </div>
    </div>
  </div>

  {/* 右侧详情区 */}
  <div style={{ flex: 1, overflow: 'hidden', minWidth: 0 }}>
    {selected ? <DetailView /> : <EmptyState />}
  </div>
</div>
```

**空状态**（右侧无选中时）：
```tsx
<div style={{ height: '100%', display: 'flex', flexDirection: 'column',
  alignItems: 'center', justifyContent: 'center', gap: 12 }}>
  <div style={{ color: c.border, fontSize: 40 }}>←</div>
  <div style={{ color: c.muted, fontSize: 14 }}>选择左侧记录查看详情</div>
</div>
```

---

### Pattern B：全屏纵向（工具条 + 滚动主体）

用于仪表盘、详情嵌套、全屏进度视图。

```tsx
<div style={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
  {/* 固定顶部工具条 */}
  <div style={{ padding: '8px 16px',
    borderBottom: `1px solid ${c.border}`,
    display: 'flex', alignItems: 'center', gap: 12, flexShrink: 0 }}>
    ...
  </div>

  {/* 可滚动主体 */}
  <div style={{ flex: 1, overflowY: 'auto', padding: 16,
    display: 'flex', flexDirection: 'column', gap: 12 }}>
    {/* 指标卡片行 */}
    <div style={{ display: 'flex', gap: 10, flexWrap: 'wrap' }}>
      <MetricCard ... />
    </div>
    {/* 2 列图表 */}
    <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr', gap: 12 }}>
      <ChartCard title="图表1">...</ChartCard>
      <ChartCard title="图表2">...</ChartCard>
    </div>
    {/* 全宽图表 */}
    <ChartCard title="图表3">...</ChartCard>
  </div>
</div>
```

---

### Pattern C：单列表单（居中，限宽）

用于表单页、配置页、创建/编辑视图。

```tsx
<div style={{ minHeight: 'calc(100vh - 52px)' }}>
  <div style={{ maxWidth: 800, padding: '32px 40px', boxSizing: 'border-box' }}>
    {/* 页头：返回 + 标题 */}
    <div style={{ display: 'flex', alignItems: 'center', gap: 16, marginBottom: 24 }}>
      <button ...>← 返回</button>
      <div>
        <h2 style={{ color: c.text, fontSize: 20, fontWeight: 700, margin: 0 }}>标题</h2>
        <p style={{ color: c.muted, fontSize: 12, margin: 0 }}>副标题</p>
      </div>
    </div>

    {/* 表单区块（FormBlock） */}
    <div style={{
      border: `1px solid ${c.border}`,
      borderRadius: 12,
      padding: '20px 24px',
      marginBottom: 16,
    }}>
      {/* 3 列参数网格 */}
      <div style={{ display: 'grid', gridTemplateColumns: '1fr 1fr 1fr', gap: 12 }}>
        ...
      </div>
    </div>

    {/* 提交行 */}
    <div style={{ display: 'flex', gap: 12, alignItems: 'center', paddingBottom: 48 }}>
      <input style={{ flex: 1, ...inp }} />
      <button style={{ background: c.primary, ... }}>提交</button>
    </div>
  </div>
</div>
```

**常用内部网格**：

| 场景 | gridTemplateColumns |
|------|---------------------|
| 3 列数字参数 | `1fr 1fr 1fr` |
| 2 列字段 | `1fr 1fr` |
| 主字段 + 操作按钮 + 次字段 | `flex: 1.5` + `flexShrink: 0` + `flex: 1` |

---

## 卡片规范

### MetricCard（数据指标卡）

```tsx
<div style={{
  border: `1px solid ${c.border}`,
  borderRadius: 10,
  padding: '14px 20px',
  textAlign: 'center',
  minWidth: 96,
}}>
  <div style={{
    color: c.muted, fontSize: 11, fontWeight: 600,
    textTransform: 'uppercase', letterSpacing: '0.07em', marginBottom: 6
  }}>
    {label}
  </div>
  <div style={{
    color: c.text, fontSize: 24, fontWeight: 700,
    letterSpacing: '-0.02em', lineHeight: 1
  }}>
    {value}
  </div>
</div>
```

### ChartCard（图表容器卡）

```tsx
<div style={{
  border: `1px solid ${c.border}`,
  borderRadius: 10,
  padding: '14px 16px',
}}>
  <div style={{
    color: c.muted, fontSize: 11, fontWeight: 600,
    marginBottom: 8, letterSpacing: '0.07em', textTransform: 'uppercase'
  }}>
    {title}
  </div>
  {children}
</div>
```

### FormBlock（表单区块）

```tsx
<div style={{
  border: `1px solid ${c.border}`,
  borderRadius: 12,
  padding: '20px 24px',
  marginBottom: 16,
}}>
```

---

## 间距原则

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

## 主题系统

所有颜色必须通过 theme provider 获取，**禁止硬编码颜色值**。

```tsx
import { useTheme } from './theme'

function MyComponent() {
  const { tokens: c } = useTheme()
  // 使用 c.text, c.border 等 token
}
```

### Token 命名约定（项目级约定示例）

| Token | 用途 |
|-------|------|
| `c.bg` | 页面背景 |
| `c.surface` | 主要卡片面 |
| `c.panel` | 侧边栏/面板背景 |
| `c.border` | 边框/分割线 |
| `c.text` | 主要正文 |
| `c.muted` | 辅助说明文字 |
| `c.primary` | 主色（按钮/选中态） |
| `c.accent` | 强调色（警告/错误） |
| `c.cardBg` | 数据卡片背景 |
| `c.inputBg` | 输入框背景 |
| `c.rowHover` | 表格行悬停背景 |

**项目适配**：在你的 `theme.tsx` 中定义 dark / light 两套 token 对象，确保两套颜色对比度均满足 WCAG 要求。

---

## 状态色徽章模式

状态色使用 rgba 半透明背景 + token 文字色，两套主题下通用。

```tsx
// 成功/运行中
{ bg: 'rgba(74,222,128,0.12)', color: c.green, border: 'rgba(74,222,128,0.3)' }
// 主色/已完成
{ bg: 'rgba(99,102,241,0.12)', color: c.primary, border: 'rgba(99,102,241,0.3)' }
// 错误/已中止
{ bg: 'rgba(248,113,113,0.12)', color: c.red, border: 'rgba(248,113,113,0.3)' }
// 警告
{ bg: 'rgba(251,191,36,0.1)', color: c.yellow, border: 'rgba(251,191,36,0.3)' }
// 紫色标签
{ bg: 'rgba(167,139,250,0.1)', color: c.purple, border: 'rgba(167,139,250,0.25)' }
```

徽章通用样式：`fontSize: 11, fontWeight: 600, padding: '2px 8px', borderRadius: 4`

---

## 表单元素样式

### 输入框

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
```

### 标签（Label）

```tsx
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

## 对比度要求（WCAG AA ≥ 4.5:1 / AAA ≥ 7:1）

| 级别 | Token | 说明 |
|------|-------|------|
| AAA ≥ 7:1 | `text` | 主要正文必须满足 |
| AAA ≥ 7:1 | `textSub` | 次要正文 |
| AA ≥ 4.5:1 | `muted` | 标签、辅助说明 |
| 推荐 | `label` | 时间戳等非关键文字 |

亮暗两套主题的 token 值需分别测试对比度。

---

## 禁止事项

- 禁止硬编码颜色值（如 `#0f3460`、`#16213e`）
- 禁止在组件内直接写死 `background: '#xxx'`，必须用 token
- 新增颜色语义时，先在 theme.tsx 的 dark/light 对象中同时添加 token
- 不要为 `label` 级别文字强求 7:1 对比度

---

## 快速参考卡

```
颜色获取：const { tokens: c } = useTheme()

背景类  → c.bg / c.surface / c.cardBg
边框    → c.border
文字    → c.text / c.muted / c.label
交互    → c.primary / c.primaryHover / c.rowHover
状态    → c.green / c.red / c.yellow / c.purple

间距：
  gap: 10-12px     padding(数据卡): 14px 20px
  padding(图表卡): 14px 16px    padding(表单块): 20px 24px
```
