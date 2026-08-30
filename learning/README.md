# learning/

Leon 的个人学习记录目录，不属于产品代码与文档体系，不受 docs/ 的双语与文档门禁约束。

## 分支设计

```text
deepseek-ai/deepseek-harness (upstream)
      │  fetch（只拉不推，push 地址已设为 no_push）
      ▼
   master ──push──►  origin/master   纯镜像，永远不在这里提交
      │  rebase 基线
      ▼
    diy   ──push──►  origin/diy      master + learning/，只有这个目录是我加的
```

| 分支 | 跟踪 | 用途 | 规则 |
| --- | --- | --- | --- |
| `master` | `origin/master` | 同步 `deepseek-ai/deepseek-harness` 的原始代码 | 只做快进，不手写提交 |
| `diy` | `origin/diy` | 在 `master` 之上叠加学习记录 | 改动只写在 `learning/` 里 |

两个远端：

- `origin` = `ltlovezh/deepseek-harness`（我的 fork，`master` / `diy` 都推这里）
- `upstream` = `deepseek-ai/deepseek-harness`（原始仓库，push 地址已禁用，防止误推）

diy 的全部改动都收在 `learning/` 一个目录里，上游永远不会碰到这个路径，所以 rebase 到新的 `master` 上不会有冲突。

> 另有两个历史分支：`diy-backup`（diy 的旧快照）和 `claude/deepseek-harness-setup-da681f`。都不参与同步流程，确认无用后可以删掉。

## 同步上游

一条命令走完「拉上游 → 快进 master → 推 origin/master → 把 diy rebase 到新 master → 推 origin/diy」：

```bash
./learning/sync.sh
```

需要在 `diy` 分支上运行——`learning/` 只存在于 `diy`，切到 `master` 后这个文件在工作区里就没了。想在任何分支上跑：

```bash
git show diy:learning/sync.sh | bash
```

如果 `master` 上不小心提交过东西导致无法快进，脚本会报错、切回原分支并退出，不会硬来。

手动等价操作：

```bash
git fetch upstream
git switch master && git merge --ff-only upstream/master && git push origin master
git switch diy && git rebase master && git push --force-with-lease origin diy
```

`diy` rebase 后历史会被改写，所以推送需要 `--force-with-lease`（远端有意外变动时它会拒绝推送，比 `--force` 安全）。

## 索引

- [dsh-learning-map.html](dsh-learning-map.html) — DSH 学习地图：从 harness 小白到插件作者的九关三篇章递进学习计划，含可勾选的进度打卡（存浏览器本地）、每关「目标/读/动手/检查点/深水区」四件套、DSH ↔ Claude Code ↔ Codex 概念对照表、命令速查与内外参考资料。**从这里开始。**
- [dsh-agent-loop.html](dsh-agent-loop.html) — Agent Loop 核心流程图解：七行伪代码内核 + 五遍阅读阶梯，六张源码对照图（会话日志、状态机与收件箱、turn/step 主循环、工具并发调度、工具执行流水线、结果回流闭环）。学习地图关 0 与关 5 的伴读教材。
