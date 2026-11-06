---
name: Sumer
description: 在前后端配对的开发工作树中做需求、修 bug、联调时使用。只要任务可能涉及前端改动、需要把前端活派给同分支的前端工作树会话、用户提到 agentteam / 并行 / 分派 / handoff、要做 review，或要用 ego-lite 做端到端点击测试，就先读本 skill。纯问答、只读排查、状态查询不要用。
disable-model-invocation: true
---

# 开发分派

在后端工作树做指挥：前端交给同分支的前端工作树，后端按需开团队，review 交给独立 subagent，E2E 用 ego-lite。

```
0 开工 → 1 判定 ┬ 2 前端分派 ┐
                └ 3 后端实现 ┴→ 4 Review → 5 E2E → 6 交付
```

## 角色

按工作树内容和消息来源判定，不看仓库名。

- 消息以 `[dispatch:` 开头：执行方。直接干活，不再分派，遵守本仓库 `AGENTS.md` 和 `docs/`。
- 工作树有 `package.json` + 前端构建配置（vite / next 等）：前端执行方。直接干活，完成后按第 4 节 review。
- 工作树有 `manage.py` 等后端入口：指挥方。走第 0–6 节。

两种特征都有时按任务改动栈判定；拿不准只问一个问题。

## 前端实现约束

所有前端改动都适用，不读外部提示词文件：先读 `AGENTS.md` 和 `docs/`；复用现有组件和 Token；规范缺失、冲突或不确定时向用户提出，不自行定义样式或加新值。

## 0. 开工

1. 读根 `AGENTS.md` 和目标目录沿途的 `AGENTS.md`。
2. 记录 `git rev-parse --show-toplevel`、`git branch --show-current`、`git status --short`。前端必须同分支。
3. 一句话写清目标、不做的事、验收证据。写不出验收证据就先问。

## 1. 判定

| 工作内容 | 去向 |
|---|---|
| 页面、交互、样式、前端状态、接口字段消费 | 第 2 节 |
| 接口、权限、数据、任务 | 第 3 节 |
| 两端都有 | 第 2、3 节并行 |
| 拿不准 | 只问一个问题 |

## 2. 前端分派

读 `references/frontend-dispatch.md`。只派给分支与后端相同的前端工作树；先只读握手核对，再派活。找不到会话就给启动命令，不自己代做前端。发完不轮询。

## 3. 后端实现

默认主会话自己做。只有用户明确说“开 agentteam / 并行 / 用团队”才开。

开团队时读 `assets/handoff-template.md`，拆成可独立完成的部分。handoff 写进 teammate 的 prompt；长任务或多人共享时落到 `.claude/handoffs/<task-slug>.md`。用 `Agent(name: "be-1", ...)` 并行 spawn；有文件交集的活留主会话，完成后主会话集成验证。

## 4. Review

读 `references/review.md`，spawn 独立只读 subagent。指挥方审改动涉及的工作树；有前端改动就必须做前端 review；纯前端任务审自己的 diff，不能跳过。违反 `AGENTS.md` / `docs/` 明确约束也算 P0。只报 P0、只修 P0、只跑一轮；P1+ 只报告。

## 5. E2E

读 `references/e2e.md`。固定后端 18001、前端 15173；同分支服务已跑就直接复用、不授权，需要自己启动或连库才授权。用 ego-lite 点一次主路径；未执行就写“未执行 E2E”。

## 6. 交付

后端改动、前端改动（来自 `[fe-report]`）、review 的 P0、E2E 结果、未验证项、阻塞。`CHANGELOG.md` 和 `MEMORY.md` 按后端 `AGENTS.md` 的触发条件询问。
