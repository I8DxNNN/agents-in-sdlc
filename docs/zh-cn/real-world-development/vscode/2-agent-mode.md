---
title: "练习 2 - 使用 Copilot 代理模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是简单的应用更新，通常也需要修改多个文件并执行测试等操作。开发者通常需要找到所有相关文件、进行修改、运行测试、调试，再查找遗漏的文件并继续修改，反复进行。Copilot 代理模式可以协助完成这些工作。

Copilot 代理模式能够在 IDE 中更自主地工作。它会先探索现有项目结构，执行所需更新，运行测试等任务，并自动修复发现的问题。接下来了解如何使用代理模式为网站添加新功能。

> [!NOTE]
> 代理模式和云智能体的名称相似，但适用于不同的工作方式。代理模式在 IDE 中执行任务，支持快速反馈和交互。云智能体则像异步工作的团队成员，通过议题和拉取请求与你协作。

本练习将学习如何：

- 让 Copilot 代理模式探索项目、识别相关文件并协调修改。
- 使用 Copilot 代理模式跨 UI 和数据层实现新功能。
- 在将代码合并到项目之前，审查 Copilot 代理模式生成的更改和测试。

## 场景

随着游戏列表不断增长，你希望用户能够按类别和发行商筛选游戏。上一练习已添加 publisher 辅助函数，现在将使用 Copilot 代理模式完成其余数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

修改代码前，先探索 Tailspin Toys 网站，了解它当前的功能。

该网站是一个桌游众筹平台，采用开发者主题。用户可以查看游戏列表及其详细信息。这是一个单体 Astro 应用，会在构建时将页面渲染为静态 HTML。页面通过 Drizzle ORM 在 frontmatter 中直接查询本地 SQLite 数据库，没有单独的后端 API 或客户端 UI 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能由使用标准 DOM API 的小型 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。请在 GitHub Codespace 中按以下步骤操作：

1. 返回上一练习中使用的 Codespace。此时应已切回 `main`。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）在 Codespace 中打开新终端。
3. 创建并切换到筛选工作分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，会显示类似以下内容的横幅：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现提示你打开 `http://localhost:4321` 的对话框，请选择 **x** 将其关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后在终端中选择 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中，从终端选择 localhost URL 会自动将其重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是 Codespace 为当前 Web 服务器提供的专用隧道。

### 探索网站

网站的主要功能包括：

- **主页**：显示桌游列表，包括标题、图片和说明。
- **游戏详情页**：选择游戏后可查看标题、说明、发行商和类别等详细信息。

## 使用 Copilot 查看待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，以便开始新的对话。模式和模型选择因练习而异；每个步骤会在需要时说明。

网站已能正常运行，但还可以添加更多功能。先查看存储库待办事项。使用模板创建项目时，系统已自动创建了一组议题；请让 GitHub Copilot 显示这些议题。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。Agent 智能体可以自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器屏幕截图。](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者并未推荐任何特定模型。构建本研讨会时使用的是 Claude Sonnet 4.5，因此教程中列出了该模型，以便尽量保持建议结果一致、提供良好体验。不过，由于大语言模型具有概率性，你看到的建议可能与教程不同，这是正常现象。

> [!NOTE]
> Copilot 可能会使用不同的 MCP 命令，但仍应能够完成任务。

3. 发送以下提示，询问 Copilot 有哪些待办议题：

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. 选择 **Continue** 以运行列出所有议题的命令。
5. 查看生成的议题列表。

注意 Copilot 还根据它认为对用户最有用的事项对议题进行了排序。

## 查看说明文件

让智能体生成代码前，先查看将用于提供上下文的说明文件。本练习将使用 [UI 说明文件](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)，了解如何为网站添加功能。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，包括：
   - 应用程序架构概览。
   - 组件设计、可测试性和无障碍原则。
   - 指向 Astro、Tailwind CSS 和 Drizzle 等特定文件说明的链接。

> [!TIP]
> 说明文件可以引用其他说明文件和项目文件。路径相对于说明文件所在位置，因此可以复用内容、将大型说明拆分为较小部分，并提供示例供 Copilot 参考。

## 实现筛选功能

完成筛选至少需要对应用程序进行三项更改：

- 在数据层 (`src/lib/`) 添加或完善筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页面以加入筛选 UI。

此外，合并前必须运行并通过测试。Copilot 代理模式可以执行这些任务。

1. 可以继续当前 Copilot 对话，也可以选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions**，然后选择 **ui** 说明文件。

   ![选择 UI 说明文件的屏幕截图](../../../_images/ex3-select-instructions-file.png)

3. 确认 Chat 视图的智能体下拉列表中仍选择 **Agent**。

   ![Chat 视图中的智能体选择器屏幕截图。](../../../_images/shared-chat-mode-selector.png)

4. 确认仍选择 **Claude Sonnet 4.5** 模型。
5. 使用以下提示，请 Copilot 根据待办事项中的相关议题实现功能：

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. 观察 Copilot 如何探索项目并查找相关数据层辅助函数、UI 和测试，然后修改文件并运行测试。

   ![Copilot 正在探索项目文件的屏幕截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行探索项目、修改文件和运行测试等多项任务。根据任务复杂度和代码库规模，这可能需要几分钟。过程中编辑器中可能会显示 **Keep** 和 **Undo** 按钮。Copilot 完成后，所有更改都会显示相应的 **Keep** 或 **Undo** 控件，因此工作尚未完成时无需选择。

7. Copilot 提示时，选择 **Continue** 运行测试。

   ![Copilot Chat 窗格中提示用户确认运行测试的屏幕截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 处理中可能会暂停，测试也可能暂时失败。这没有关系。Copilot 会在生成代码和运行测试之间迭代，直到完成任务且未检测到错误。

   ![完整 Copilot 代理模式会话的屏幕截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码是否存在问题。

> [!CAUTION]
> 务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回运行网站的浏览器，探索新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

本练习介绍了如何使用 GitHub Copilot 代理模式为 Tailspin Toys 网站添加新功能。你已了解如何：

- 使用 GitHub Copilot 代理模式跨 UI 和数据层实现新功能。
- 让 Copilot 代理模式探索项目、识别相关文件并协调修改。
- 在将代码合并到项目之前，审查 Copilot 代理模式生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为其创建拉取请求。

### 拓展练习——实现分页

随着游戏列表不断增长，需要实现分页。可以运用本练习中的技能提示 Copilot 更新网站。编写代码时请考虑：

- 遵循现有最佳实践，包括使用已有说明文件。
- 确定分页方式：允许用户选择每页大小，还是使用固定大小。
- 在提示中提供必要的指导，确保 Copilot 按照预期实现功能。
- 可能需要与 GitHub Copilot 多次迭代，要求它修改并提供上下文。这是正常的协作流程。

## 资源

- [Copilot ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的代理模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
