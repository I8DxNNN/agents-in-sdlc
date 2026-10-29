---
title: "练习 5：迭代改进 GitHub Copilot 的成果"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

## 审查工作成果

在本实验中，你与 GitHub Copilot 合作完成了多项旨在改善用户体验和添加功能的任务：为代码添加文档、实现供设计团队迭代的相关游戏功能，以及添加高对比度和浅色模式切换等无障碍功能。现在查看代码更改；必要时向 Copilot 提供反馈，进一步完善成果。

### 场景

如前所述，引入生成式 AI 并不会改变软件设计和 DevOps 的基本原则。仍然需要审查生成的代码，并遵循常规 DevOps 流程。接下来先审查 GitHub Copilot 为代码文档、相关游戏功能和无障碍功能生成的建议，然后再为团队其他成员启用审查。

## 安全与 GitHub Copilot 云智能体

Copilot 云智能体会在无人监督的情况下异步执行任务，因此设置了一些安全限制：

- Copilot 对存储库只有读取权限，对工作分支只有写入权限。
- 云智能体在 GitHub Actions 中运行，并创建独立的临时工作环境。
- GitHub Actions 工作流必须先经人工批准才能运行。
- 默认情况下，[对外部资源的访问受到限制](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，包括 MCP 服务器。

## 审查生成的文档

先查看 GitHub Copilot 云智能体创建的第一个拉取请求 (PR)，其中添加了代码文档。此处将使用 GitHub.com 上的标准 PR 界面。

> [!NOTE]
> 查看 PR 时，可能会看到 GitHub Copilot 被防火墙阻止的警告。这是**预期行为**，因为 Copilot 默认只能有限地访问外部资源，包括外部 MCP 服务器。如果需要，可以[自定义或禁用 Copilot 云智能体防火墙][agent-firewall]。

1. 返回 github.com 上的存储库。
2. 选择 **Pull Requests**，打开拉取请求列表。
3. 打开标题类似于 **Add missing documentation** 的拉取请求。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。请等待 Copilot 完成任务。这可能需要几分钟，可以趁此休息或回顾已学内容。

4. PR 准备好后，选择 **Files changed** 选项卡并审查更改。

    ![Files changed 选项卡](../../../_images/shared-pr-files-changed.png)

5. 查看新添加的 TSDoc 文档注释和其他文档。具体更改可能有所不同。

   浏览更改时，请留意 TSDoc 文档注释、TypeScript 规范和文件注释头。这些内容来自练习 2 开始时查看的自定义说明文件。

6. 审查更新并确认无误后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流正在等待批准。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)
9. 工作流应会进入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查就会通过。这可能需要几分钟。

## 请求 GitHub Copilot 更改

与 Copilot 协作并非单向过程。你可以像在团队成员的拉取请求或代码行内评论中那样标记 Copilot。Copilot 会读取评论，并启动新会话来处理反馈。由于结果具有非确定性，无法提供唯一的具体指令。可以要求 Copilot：

- 在每个代码文件开头添加简短说明文件用途的注释头。
- 在 TypeScript 和 Astro 文件中添加 TSDoc 文档注释。
- 创建 README，介绍 Astro 应用的结构。

1. 添加评论并像标记其他用户一样标记 **@copilot**，请求它更改生成的文档。可以使用上面的建议，也可以提出其他文档改进要求。
2. 选择 **View Session** 查看 Copilot 如何执行更改。注意 Copilot 会启动新会话来进行更新。
3. 选择 **Back to pull request** 返回拉取请求。

    ![返回拉取请求](../../../_images/ex7-back-to-pr.png)

4. Copilot 完成更改后，拉取请求中会出现新的提交。
5. 选择 **Files changed** 选项卡审查更改。

可以继续迭代，直到满意为止。完成后，可以将 PR 从草稿状态改为准备审查，然后合并到 main 分支。

![将 PR 改为准备审查](../../../_images/ex7-ready-for-review.png)

## 审查相关游戏功能

接下来返回 Copilot 为“在游戏详情页显示相关游戏”议题创建的 PR。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Show related games on the game details page** 的 PR。
4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查更新并确认无误后，返回 **Conversation** 选项卡并向下滚动。
6. 查看是否有工作流正在等待批准。
7. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)
8. 工作流应会进入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查就会通过。这可能需要几分钟。
9. **可选：**也可以在 Codespace 中切换到此分支，手动测试相关游戏功能。打开 Codespace 终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名，例如 **copilot/fix-8**）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot 已实现相关游戏功能！与之前一样，可以继续迭代并要求云智能体更新，例如调整显示的相关游戏数量，或添加文件注释头和 TSDoc 文档注释（请记住，分配此任务时尚未更新自定义说明）。可以在 **Conversation** 选项卡中添加评论，Copilot 会读取评论并启动新会话。

## 审查无障碍功能

最后，审查通过无障碍自定义智能体实现的功能。此 PR 应包括练习 3 中分配的高对比度模式，以及练习 4 在管理中心请求的浅色模式。

1. 返回 GitHub.com 上的存储库。
2. 选择 **Pull Requests** 选项卡。
3. 选择标题类似于 **Add high contrast mode to website** 的 PR。

> [!NOTE]
> 如果 Copilot 仍在处理任务，拉取请求会带有 **[WIP]** 标记。请等待 Copilot 完成任务，这可能需要几分钟。

4. 选择 **Files changed** 选项卡，审查生成的代码。
5. 审查实现，尤其留意：
   - 用于切换模式的 UI 组件。
   - 用于保存用户偏好的本地存储。
   - 高对比度和浅色模式的 CSS 或样式更改。
   - 无障碍属性（ARIA 标签、键盘导航等）。
   - 管理模式切换的 JavaScript/TypeScript 代码。
6. 审查更新并确认无误后，返回 **Conversation** 选项卡并向下滚动。
7. 查看是否有工作流正在等待批准。
8. 如果有工作流等待批准，请选择 **Approve and run workflows**。

   ![批准并运行工作流](../../../_images/shared-approve-workflows.png)
9. 工作流应会进入拉取请求的检查列表。如果一切正常，单体 Astro 应用的项目检查就会通过。这可能需要几分钟。
10. **可选：**也可以切换到 Codespace 中的此分支，手动测试无障碍功能。打开 Codespace 终端并运行以下命令（将 `<branch-name>` 替换为 Copilot 创建的分支名）：

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    然后启动应用程序，在浏览器中测试高对比度和浅色模式切换，并确认设置会在重新加载页面后保留。

无障碍自定义智能体帮助 Copilot 按照无障碍最佳实践实现这些功能。如果发现任何无障碍问题或改进点，可以像处理前面的 PR 一样，在评论中标记 **@copilot** 请求更新。

## 可选练习——继续委派任务

云智能体最适合处理实际待办事项，让你可以转而完成其他工作。继续练习这种工作方式：在存储库中创建更多议题并分配给 Copilot。可以尝试：

- 在游戏详情页创建支持者意向表单。
- 为游戏列表页实现分页。
- 为数据访问辅助函数添加输入验证和错误处理。

## 总结

你已完成云智能体学习路径。在这些课程中，你：

- **检查了此存储库自带的自定义说明文件**，以便之后查看它们对云智能体输出的影响。
- **将议题分配给 Copilot 云智能体**，观察它设置环境、制定计划并异步执行任务。
- **创建并使用自定义智能体**来处理无障碍问题，并添加高对比度和浅色模式切换。
- **使用 Copilot 智能体页面作为管理中心**，监控并引导正在进行的无障碍会话。
- **审查并迭代云智能体创建的拉取请求**，标记 `@copilot` 请求更改并批准工作流。

## 回顾和后续步骤

你已完成云智能体学习路径。若想继续探索，其他学习路径可以补充本次实践：

- 🖥️ **[VS Code 学习路径](../../vscode/)**——在 IDE 中探索 Copilot Chat 代理模式和 MCP 集成。
- 💻 **[CLI 学习路径](../../cli/)**——在终端中交付两项经过审查的更改，体验 Plan 和 Autopilot 模式、智能体技能、自定义 QA 智能体、Playwright MCP、Agent Merge，以及可选的 `/delegate` 云智能体桥接。

你还可以在自己的存储库中尝试以下后续任务：

- 将重构或测试覆盖率议题分配给云智能体。
- 为其他领域创建自定义智能体。
- 为不同的技术栈设置 `copilot-setup-steps.yml`。

## 资源

- [GitHub Copilot][github-copilot]
- [关于 Copilot 智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳做法][cloud-agent-best-practices]
- [配置 Copilot 云智能体防火墙][agent-firewall]

---

| [← 上一课：管理智能体][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
