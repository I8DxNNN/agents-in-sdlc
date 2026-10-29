---
title: "课程 9 - GitHub Copilot CLI 斜杠命令"
description: "探索用于管理上下文、选择模型、共享会话和可选委派到云端的斜杠命令。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

与其他优秀 CLI 工具一样，GitHub Copilot CLI 提供许多可用于交互的斜杠命令。这些命令提供高级功能、幕后信息和其他配置选项。你已使用过 `/diff`、`/mcp`、`/skills`、`/agent` 和 `/pr` 等命令，现在来探索其他实用命令。

本课将学习如何：

- 使用 `/context` 和 `/compact` 了解 Copilot 如何管理对话上下文。
- 使用 `/model` 探索可用模型。
- 了解如何使用 `/share` 导出或共享会话。
- 探索并行工作、工作树和委派到云智能体等可选命令。

## 场景

CLI 核心工作流已经完成，现在了解一些其他功能：管理上下文、切换模型、共享会话，以及选择是否将任务委派给 [Copilot 云智能体][about-cloud-agent]。

## 探索 Copilot CLI 上下文

处理较大或较复杂的任务时，可能会达到模型的最大上下文窗口。Copilot CLI 会在需要时自动压缩对话，你也可以使用斜杠命令检查或自行压缩上下文。

1. 返回 Codespace；如果 Copilot CLI 尚未打开，请从存储库根目录启动。
2. 输入：

   ```plaintext
   /context
   ```

3. 注意模型、当前令牌用量，以及上下文在系统说明、工具、消息和可用空间之间的分布。
4. 压缩对话：

   ```plaintext
   /compact
   ```

5. 再次输入 `/context` 并比较结果。如果对话本来就很短，变化可能不明显。

> [!NOTE]
> 上下文窗口接近上限时，Copilot CLI 会自动压缩上下文。想自行决定压缩时机时可使用 `/compact`。如果要切换到无关任务并开始全新对话，则使用 `/clear` 或 `/new`。

## 选择模型

不同模型有不同优势，开发者的偏好也各不相同。Copilot CLI 可以列出模型并让你选择。

1. 输入：

   ```plaintext
   /model
   ```

2. 浏览可用模型和用量信息。
3. 保留当前模型、选择其他模型，或按 <kbd>Esc</kbd> 关闭列表。

## 共享会话

团队合作并分享经验有助于所有人更好地使用 AI 工具。`/share` 命令可将会话导出为 Markdown 或 HTML 文件、创建可共享链接，或发布 GitHub gist。

1. 输入 `/help`，查看你所安装版本中的 `/share` 选项。
2. 如果要共享此会话，请选择合适的目标，例如 `/share file` 用于导出本地 Markdown 文件。
3. 发送给其他人或发布前，请审查导出内容。会话导出可能包含提示、响应和项目详情。

发布链接或 gist 是可选操作。不要发布团队不打算共享的存储库或对话内容。

## 可选：扩展并行工作或委派任务

核心研讨会已完成。Copilot CLI 还为大型任务提供了以下命令：

- `/fleet` 可以将独立子任务交给多个子智能体并行执行。
- `/worktree` 可以为不同任务创建隔离的 Git 工作树。
- `/delegate` 可以将任务发送给 Copilot 云智能体，由它异步工作并可能创建拉取请求。

这些命令是可选的，因为它们可能创建额外工作树或远程工作。尝试前先创建全新且范围明确的任务，并按常规流程审查结果。若要深入了解异步智能体工作，可继续学习[云智能体研讨会][cloud-workshop]。

## 总结和后续步骤

Copilot CLI 斜杠命令可用于配置 CLI、共享会话并查看其幕后运行情况。本课中，你：

- 使用 `/context` 和 `/compact` 了解 Copilot 如何管理对话上下文。
- 使用 `/model` 探索可用模型。
- 了解如何使用 `/share` 导出或共享会话。
- 探索并行工作、工作树和委派到云智能体等可选命令。

Copilot CLI 还有更多斜杠命令可供探索。现在通过[回顾所学内容][next-lesson]和后续步骤结束本次学习。

## 资源

- [Copilot CLI 命令参考][cli-reference]
- [Copilot CLI 中的上下文管理][context-management]
- [关于 Copilot 云智能体][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
