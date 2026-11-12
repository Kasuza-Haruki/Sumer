# 前端分派

## 目标

派给**分支名与后端当前分支相同的前端工作树**。不同分支不派，即使目录名相同。同分支多个时，选目录名与后端工作树相同的那个。用工作树内容和分支判断，不看仓库名。

## 找会话与握手

先本地定位，不发消息：

1. `ListAgents` 取活跃 name。
2. 读 `~/.claude/sessions/*.json`，只保留 name 在活跃列表里的项，得到 `cwd`。
3. 对每个 `cwd` 只读检查：有 `package.json` 和前端构建配置，且 `git -C <cwd> branch --show-current` 等于后端分支。
4. 唯一命中就用它的 name 发握手；多个命中选目录名与后端工作树相同的；同一 `cwd` 有多个会话优先 idle；仍不唯一就问用户一次。
5. 没有命中就问用户前端工作树或会话名。不群发、不按名字猜；`~/.claude/sessions` 不存在时让用户指定会话名。

只向命中的会话发只读握手：

```
[dispatch:handshake] 不要改任何文件。用 SendMessage 回复给本消息的发送方，消息以 [dispatch:handshake-reply] 开头，正文只含三行：
git rev-parse --show-toplevel
git branch --show-current
git status --short
```

只在本会话输出不算回复；指挥方看不到。

核对：toplevel 有 `package.json` 和前端构建配置；分支与后端完全相同；多候选时目录名与后端工作树相同。不一致就停下报告，不让对方切分支。

没有可用会话时给用户：

```bash
cd <前端工作树>
git switch <后端分支>     # 仅当分支不一致且用户确认
claude
```

前端仓库根和工作树位置从配对约定或用户处确认，不确定就问一次。本地没有该分支时报告。

## 契约与派活

涉及两端时，先把路径、入参、响应字段、错误格式写进消息，不让两端各自猜。

```
[dispatch:<branch>]
任务：...
验收：...
非目标：...
契约：<涉及接口时给路径、入参、响应字段、错误格式>
必读：先加载 Sumer skill，按执行方规则执行；AGENTS.md；docs/ 设计规范与本任务相关文档
约束：复用现有组件和 Token；规范不确定时提问，不自行加样式；不改无关文件；不 commit/push；Review 由指挥方统一做
回报：完成后用 SendMessage 发给本消息的发送方，消息以 [fe-report] 开头，给分支、改动文件、git diff --stat、验证命令与结果、阻塞。只在本地输出不算回报。
```

发完继续做后端，不轮询。收到 `[fe-report]` 后核对分支、文件和 `git diff --stat`；不完整就问，必要时自己读前端工作树。
