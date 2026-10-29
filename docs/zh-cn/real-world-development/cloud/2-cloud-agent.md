---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都会遇到技术债务，例如尚未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。完成后，它会创建草稿 PR，供开发者审查。这样可以将繁琐任务交给智能体，加快开发进程，让开发者专注于更重要的工作。

本课将使用 Copilot 云智能体探索以下内容：

- 自定义代码生成环境。
- 确保操作安全。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 有一些待处理的技术债务。最初为网站开发的承包商没有留下文档，因此团队希望先为应用程序中所有导出的函数添加 TSDoc 文档注释。

此外，设计团队希望改进游戏发现体验：在每款游戏的详情页上显示同类别的其他游戏，以便用户继续浏览。目前不要求实现精致，只需可以用于 UX 验收测试。该任务目前受阻，优先级低于其他议题。

这类任务很容易被不断推迟，正适合交给 Copilot 云智能体。它可以异步处理任务，让开发者继续处理其他工作，之后再回来审查成果。

## 认识 GitHub Copilot 云智能体

[GitHub Copilot 云智能体](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent)可以像开发者一样在后台执行任务。你可以通过多种方式启动任务，例如[将 GitHub 议题分配给 Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)。分配后，Copilot 会创建草稿拉取请求以跟踪进度、设置环境并开始处理任务。你可以在会话进行中或结束后查看过程。解决方案准备好供你审查时，Copilot 会在拉取请求中标记你。

## 明确界定任务范围的重要性

GitHub Copilot 并非魔法，不能替你免去思考。即使看似简单的操作，也可能包含复杂细节，因此请注意为云智能体[明确界定任务范围][cloud-agent-best-practices]。把它当作 AI 结对编程伙伴：分阶段工作、学习、试验并调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 本存储库中的自定义说明

前面的练习介绍了自定义说明及其对 Copilot 的指导作用。若你已完成自定义说明练习，应该见过 `.github/copilot-instructions.md`；如果没有，现在可以快速查看。分配任务前，先只读查看存储库中的说明文件，以便之后识别它们的影响。

在 GitHub 网页界面或已启动的 Codespace 中打开以下文件：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，尤其是 TypeScript 规范和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它会将这些说明限定到 `**/*.test.ts` 文件。

在下一节将 `Code lacks documentation` 议题分配给云智能体时，留意拉取请求中的 TSDoc 文档注释、TypeScript 规范和文件注释头；它们都来自这些说明文件。后续练习审查 PR 时还会再次说明这一点。

## 为 Copilot 云智能体设置开发环境

无论由谁编写代码，通常都需要准备特定环境并运行设置脚本，确保环境正常。将任务分配给以类似软件工程师方式工作的 Copilot 时也是如此。

云智能体使用 [GitHub Actions][github-actions] 执行任务。你可以通过 `.github/workflows/copilot-setup-steps.yml` 中配置的[专用设置工作流][setup-workflow]来自定义环境，让智能体使用所需的开发工具和依赖项。实验已预先配置此工作流，以确保流程顺利并提供学习机会。它会为单体 Astro 应用安装 Node.js、项目依赖项和端到端测试所需的浏览器，并迁移和填充 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的“Actions”选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 为这些步骤设置所需的最低权限。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果要在设置步骤中克隆存储库（例如安装依赖项），需要 `contents: read` 权限。如果未在设置步骤中克隆存储库，步骤完成后 Copilot 会自动为你克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端/应用设置 - Node.js（整个应用现在基于 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试时有可用数据。
      - name: Set up the database
        run: npm run db:setup
```

这与其他 GitHub 工作流类似，但有几点值得注意：

- 它只有一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理 PR 前，GitHub Actions 会运行此作业。
- 其中的 `workflow_dispatch` 触发器允许你在存储库的 **Actions** 选项卡中手动运行工作流，以便测试，而不必等待 Copilot 启动它。

## 添加文档

大家都知道文档很重要，但多数项目的信息已经过时或根本缺少文档。这类技术债务常常无人处理，会拖慢效率，让代码库更难维护，也让新成员更难加入。Copilot 擅长生成文档，因此很适合处理这个议题。它会在后台创建所需文档，之后的练习会审查其成果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. 将 **Title** 设置为 `Code lacks documentation`。
6. 将 **Description** 设置为：

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新创建的拉取请求 (PR)，标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中尚未显示新 PR，请稍等片刻并刷新浏览器。
12. 几分钟后，Copilot 应会创建待办事项列表。

> [!NOTE]
> Copilot 可能需要几分钟才会在 PR 中显示待办事项列表，因为它需要创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的方案。

13. 查看列表及它将要完成的任务。
14. 向下滚动 PR 时间线，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

  ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新页面才能看到更新后的状态指示器。

16. 浏览实时会话，了解 Copilot 如何解决问题，包括探索代码、了解当前状态、暂停思考并确定方案，以及编写代码。

这通常需要几分钟。Copilot 云智能体的主要优势之一是异步执行任务，让我们可以专注于其他工作。接下来，我们会利用这一点：先向 Copilot 云智能体分配另一个任务，然后为应用程序编写代码、添加功能。

## 在游戏详情页中添加相关游戏部分

GitHub Copilot 云智能体的一大优势是可以拆分工作：你专注于一组任务时，它可以处理另一组。为设计团队添加相关游戏部分可能不需要很长时间，但也可以把这段时间用于其他工作。现在就将此任务分配给 Copilot 云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. 将 **Title** 设置为 `Show related games on the game details page`。
6. 将 **Description** 设置为：

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot** 打开分配对话框。

  ![将议题分配给 Copilot](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

不久后，议题的首条评论中会出现一组 👀，表示 Copilot 已开始处理。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项请求。它的工作方式与软件工程师类似，你不必持续监控，等任务完成后再审查即可。现在转而了解如何创建和使用自定义智能体。

## 总结和后续步骤

本课介绍了 [GitHub Copilot 云智能体][copilot-agents]。你可以将议题分配给云智能体，让它异步处理技术债务、创建新功能，或协助将代码迁移到其他框架。

本课探索了以下内容：

- 自定义代码生成环境。
- 确保操作安全。
- 明确界定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体在后台工作时，我们可以开始创建和使用自定义智能体。[Copilot 云智能体还可以使用 MCP 服务器][cloud-agent-mcp]，并支持自定义说明；这些内容已在前面的模块中介绍。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳做法][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
