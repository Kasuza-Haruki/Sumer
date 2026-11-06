# Sumer

Claude Code skill：在前后端配对的开发工作树中做需求、修 bug、联调。

## 能做什么

- 后端工作树做指挥，前端派给**分支与后端相同**的前端工作树。
- 用户明确要求时，用 agentteam 拆独立任务并行。
- 前后端完成后，spawn 独立只读 subagent 做 review，只报 P0。
- 用 ego-lite 做端到端点击测试；同分支服务已在跑时直接复用，不重复授权。
- 纯前端任务也包含独立 review，不跳过。

## 安装

放到 Claude Code 用户技能目录：

```bash
git clone https://github.com/<owner>/Sumer.git ~/.claude/skills/Sumer
```

Windows 默认位置：

```
C:\Users\<you>\.claude\skills\Sumer\
```

确认 `SKILL.md`、`references/`、`assets/` 在该目录下。

## 使用

在 Claude Code 输入 `/Sumer` 调用。技能设置了 `disable-model-invocation: true`，只能由用户手动触发，模型不会自动调用。

## 结构

```
Sumer/
├── SKILL.md
├── references/
│   ├── frontend-dispatch.md
│   ├── review.md
│   └── e2e.md
├── assets/
│   └── handoff-template.md
└── evals/
    └── evals.json
```

`references/` 和 `assets/` 按需加载；`evals/` 只用于测试，不会进入模型上下文。
