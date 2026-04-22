# ui-style 技能重设计规范

**日期**：2026-04-22  
**目标**：优化技能结构可读性 + 新增 Table 和 Button 场景覆盖

---

## 问题

现有 `misc/skills/ui-style/SKILL.md` 约 400 行，单文件无优先级分层。AI 触发技能后需从头扫描才能定位关键规则，代码示例（完整 JSX 骨架）占据大量篇幅但信息密度低。

---

## 目标

1. 规则前置：核心约束 AI 最先读到
2. 压缩示例：去掉完整 JSX 骨架，只保留样式对象和关键尺寸值
3. 新增覆盖：Table（数据表格）和 Button 系统

---

## 文件结构

单文件，目标约 280 行。章节顺序：

```
1. 核心原则 + 禁止事项
2. 速查卡（token + 间距，两个紧凑表格）
3. 整体页面结构（ASCII 图）
4. 布局 Pattern A/B/C
5. 卡片规范
6. 表单元素
7. 状态色徽章
8. 数据表格（新增）
9. 按钮系统（新增）
10. 对比度要求
```

---

## 各章节内容规范

### 1. 核心原则 + 禁止事项

移至文件顶部（现在在底部）。内容不变，顺序调整。

### 2. 速查卡

将现有"快速参考卡"和"间距原则"合并为两个表格：

**Token 速查**（保留现有内容）

**间距速查**（保留现有内容）

### 3. 整体页面结构

保留 ASCII 结构图和 TopNav 说明，去掉视图切换状态表（过于项目特定）。

### 4. 布局 Pattern

每个 Pattern 从"完整 JSX 骨架"改为"关键参数表 + 一句结构说明"：

**Pattern A（左右分栏）**：
| 参数 | 值 |
|------|----|
| 容器高度 | `calc(100vh - 52px)`, `overflow: hidden` |
| 左侧宽度 | `26%`, `minWidth: 320`, `maxWidth: 400` |
| 列表项 padding | `12px 16px` |
| 选中态 | `borderLeft: 3px solid c.primary`, `bg: rgba(primary, 0.15)` |
| 悬停态 | `bg: c.rowHover` |

**Pattern B（全屏纵向）**：
| 参数 | 值 |
|------|----|
| 工具条 padding | `8px 16px` |
| 主体 padding | `16px` |
| 主体 gap | `12px` |
| 2 列图表 | `gridTemplateColumns: 1fr 1fr`, `gap: 12px` |

**Pattern C（单列表单）**：
| 参数 | 值 |
|------|----|
| 最大宽度 | `maxWidth: 800` |
| 外边距 | `padding: 32px 40px` |
| 页头 marginBottom | `24px` |

保留空状态代码（结构简单，有参考价值）。

### 5. 卡片规范

去掉 `<div>` 包装，只保留样式对象：

```tsx
// MetricCard
{ border: `1px solid ${c.border}`, borderRadius: 10, padding: '14px 20px',
  textAlign: 'center', minWidth: 96 }
// 标签：color: c.muted, fontSize: 11, fontWeight: 600, textTransform: 'uppercase'
// 数值：color: c.text, fontSize: 24, fontWeight: 700, letterSpacing: '-0.02em'

// ChartCard
{ border: `1px solid ${c.border}`, borderRadius: 10, padding: '14px 16px' }
// 标题：color: c.muted, fontSize: 11, fontWeight: 600, textTransform: 'uppercase'

// FormBlock
{ border: `1px solid ${c.border}`, borderRadius: 12, padding: '20px 24px', marginBottom: 16 }
```

### 6. 表单元素

保留现有 `inp` 和 `lbl` 样式对象（本身就是样式对象，密度合适）。

### 7. 状态色徽章

保留颜色表格，去掉重复的文字说明，只保留一句原则。

### 8. 数据表格（新增）

规则：

- 列头：`background: c.surface`, `fontSize: 11`, `fontWeight: 600`, `textTransform: uppercase`, `padding: 8px 12px`, `borderBottom: 1px solid c.border`
- 数据行：`padding: 10px 12px`, `borderBottom: 1px solid c.border`, 悬停 `background: c.rowHover`
- 选中行：`borderLeft: 3px solid c.primary`, `background: rgba(primary, 0.1)`（与列表项一致）
- 操作列：右对齐，icon 按钮默认 `opacity: 0`，行 hover 时 `opacity: 1`
- 空状态：居中，`color: c.muted`, `fontSize: 14`
- 分页条：`padding: 12px 16px`, `borderTop: 1px solid c.border`, flex 两端对齐（总数左，页码右）

```tsx
// 列头样式
const thStyle: React.CSSProperties = {
  background: c.surface,
  color: c.muted, fontSize: 11, fontWeight: 600,
  textTransform: 'uppercase', letterSpacing: '0.06em',
  padding: '8px 12px', borderBottom: `1px solid ${c.border}`,
  textAlign: 'left',
}

// 数据行样式
const tdStyle: React.CSSProperties = {
  padding: '10px 12px',
  borderBottom: `1px solid ${c.border}`,
  color: c.text, fontSize: 13,
}
```

### 9. 按钮系统（新增）

4 种变体：

| 变体 | background | color | border |
|------|-----------|-------|--------|
| primary | `c.primary` | `#fff` | none |
| secondary | `c.surface` | `c.text` | `1px solid c.border` |
| ghost | `transparent` | `c.muted` | none |
| danger | `rgba(248,113,113,0.12)` | `c.red` | `1px solid rgba(248,113,113,0.3)` |

3 种尺寸：

| 尺寸 | height | padding | fontSize |
|------|--------|---------|----------|
| sm | 28px | `0 10px` | 12px |
| md | 36px | `0 14px` | 13px |
| lg | 44px | `0 20px` | 14px |

通用样式：`borderRadius: 8`, `fontWeight: 600`, `cursor: pointer`, `transition: opacity 0.15s`

禁用态：`opacity: 0.4`, `cursor: not-allowed`

Hover 态：primary/danger 用 `opacity: 0.85`，secondary/ghost 用 `background: c.rowHover`

### 10. 对比度要求

保留现有表格，不变。

---

## 预期效果

- 文件从 ~400 行压缩到 ~280 行
- 核心约束（禁止硬编码颜色）在文件前 20 行内出现
- 新增 Table 和 Button 两个完整场景
- 布局 Pattern 保留结构参考价值，去掉冗余 JSX
