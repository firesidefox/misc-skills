# misc-skills

Claude Code 插件，提供工程实践相关的技能集合。

## 安装

在 Claude Code 中使用 `/plugin` 命令：

```
/plugin marketplace add firesidefox/misc-skills
/plugin install misc@misc-skills
```

## Skills

| Skill | 触发词 | 说明 |
|-------|--------|------|
| `misc` | `misc` | 主入口，聚合以下子技能 |
| `misc-shell-logging` | shell 脚本彩色输出 | 为 shell 脚本添加日志颜色、分隔线、进度提示 |
| `misc-makefile-logging` | Makefile 彩色输出 | 为 Makefile 添加阶段着色和步骤标记 |
| `misc-ui-style` | 布局、样式、UI | React Web 页面布局与样式规范 |

## 使用方法

在 Claude Code 对话中引用对应技能：

- 写 shell 脚本需要彩色输出 → 触发 `misc-shell-logging`
- 写 Makefile 需要着色 → 触发 `misc-makefile-logging`
- React Web 页面布局/样式 → 触发 `misc-ui-style`

## 结构

```
misc-skills/
├── .claude-plugin/
│   ├── marketplace.json    # Marketplace 元数据
│   └── plugin.json         # 插件配置
├── misc/
│   ├── SKILL.md            # 主技能入口
│   └── skills/
│       ├── logging-shell/
│       ├── logging-makefile/
│       └── ui-style/
└── README.md
```

## License

MIT
