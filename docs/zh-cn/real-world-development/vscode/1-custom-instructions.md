---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：代理模式 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能访问这些上下文。[说明文件][instruction-files]可为 Copilot 提供指导，让它了解你希望完成什么，以及应该如何完成。

本练习将学习如何：

- 通过存储库自定义说明和按路径应用的说明文件，将项目上下文、编码指南和文档规范提供给 Copilot。
- 在当前说明文件的指导下生成筛选功能的第一个数据切片（publisher 辅助函数）。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次运行相同提示，观察生成的代码如何采用新标准。
- 将说明更新和筛选功能切片提交到 `main`，供 Copilot 在下一课中使用。

> [!CAUTION]
> 生成的代码可能不会完全遵循你设置的标准，因为 Copilot 具有非确定性。本练习旨在观察更新说明后行为的整体变化趋势，而不是要求输出逐字一致。

## 说明文件

### 场景

Tailspin Toys 有一套开发指南和要求，包括：

- 数据层必须始终配备单元测试。
- UI 应采用暗色模式并具有现代感。
- 应通过 TSDoc 文档注释为代码添加文档。
- 每个文件开头都应添加一段注释，说明文件用途。

通过说明文件，可以确保 Copilot 掌握相关信息，并按这些实践完成任务。

### 自定义说明

自定义说明可向 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。你可以指定编码规范、库，以及偏好的注释类型，从而获得更贴合需求的建议和代码片段。说明既可以应用于整个存储库，也可以应用于特定文件类型或任务。

有两类说明文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件。它应包含适用于大多数 Copilot Chat 或 CLI 请求的项目级信息，例如技术栈、项目概述、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：可针对特定任务或文件类型创建。例如，为 TypeScript、Astro、UI 组件或单元测试提供指导。

> [!NOTE]
> 在 IDE 中，说明文件仅用于 Copilot Chat 生成代码，不会影响代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时，都会使用存储库级说明文件以及带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还[支持其他规范的说明文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md。

### 管理说明文件的最佳实践

创建说明文件的详细讨论超出本研讨会范围，但示例项目展示了一种有代表性的方法。总体而言：

- 将 `copilot-instructions.md` 限定为项目级指导，例如项目内容、项目结构和全局编码标准。
- 使用 `*.instructions.md` 为文件类型（例如单元测试、Astro 组件和数据层）或特定任务提供指导。
- 使用自然语言，确保说明清晰，并提供代码示例说明应如何以及不应如何编写。

说明文件没有唯一正确的写法，AI 的使用方式也不止一种。通过实践可以找到适合项目的方法。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应该有一套完善的说明文件。浏览本项目中的文件时，你可能会看到它们涵盖了多种任务，例如 [UI 更新][ui-instructions]和 [Astro][astro-instructions]。
>
> Copilot 也可以帮你生成说明文件。不同产品界面的入口有所不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。相关学习路径会在适当位置说明具体操作。
>
> 想找模板或起点？可以查看包含说明文件、自定义智能体和其他资源的 [awesome-copilot][awesome-copilot]。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 探索项目中的自定义说明文件

花些时间阅读此存储库自带的说明文件：一个核心文件 `copilot-instructions.md`，以及一组适用于不同任务的 `*.instructions.md` 文件。可在编辑器或 GitHub 网页界面中打开这些文件。

1. 打开 `.github/copilot-instructions.md`。
2. 浏览文件，注意其中简要的项目说明，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。**Code standards** 下还有 **GitHub Actions Workflows** 指南。这些内容适用于你与 Copilot 的任何交互。
3. 打开 `.github/instructions` 文件夹并浏览其中内容。注意这里有 Astro 文件、Drizzle 数据层、测试等说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它指定相对于存储库根目录的 glob 模式。此处会匹配 TypeScript 测试文件，例如符合 `**/*.test.ts` 的文件。
5. 查看此文件中有关为项目编写单元测试的说明。
6. 最后打开 `.github/instructions/drizzle.instructions.md` 并滚动至底部。注意其中指向其他说明文件（例如 `unit-tests.instructions.md`）和项目现有文件的链接。这样可以将较大的说明拆分为较小、可复用的文件，并提供示例供 Copilot 参考。路径相对于说明文件所在位置，而不是存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分介绍了项目编码标准，但尚未要求为代码添加文档。接下来会添加 TSDoc 文档注释和文件注释头规则。

## 创建更改分支

遵循最佳实践，为更改创建分支。

1. 返回上一练习中的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）在 Codespace 中打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，以便开始新的对话。模式和模型选择因练习而异；每个步骤会在需要时说明。

先在当前说明文件的指导下发送提示，观察自定义说明的影响。稍后会更新说明并重新发送相同提示，以便对比结果。

1. 关闭之前练习中打开的编辑器选项卡，让 Copilot 只获取需要的上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，以便 Copilot 修改文件。

   ![Chat 视图中的智能体选择器屏幕截图。](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改辅助函数文件和测试。
6. 注意建议的辅助函数是一个类型化函数：它将 `db` 客户端作为第一个参数，并返回类型化的 publisher 数组。这来自 `.github/instructions/drizzle.instructions.md` 中应用于 `src/lib/*.ts` 的数据层规范。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件级注释头。

> [!CAUTION]
> 由于 Copilot 的结果具有概率性，它可能会在没有明确要求时添加文档注释。如果出现这种情况也没关系，本练习重点是观察更新说明后的一致性改进。

## 添加新的存储库标准

如前所述，`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。现在为存储库补充编码标准，以改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分（应在第 27 行附近）。查看其中记录的项目编码标准；目前没有关于代码文档的规则，因此生成的辅助函数不会包含文档注释。
3. 在现有标准下方添加以下 Markdown 内容，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 如上一课所示，可以在存储库级文件（`.github/copilot-instructions.md`）中添加全局指导，也可以使用 `*.instructions.md` 为特定语言、文件类型或任务提供说明。存储库级文件适合放置项目级标准，例如刚刚添加的文档注释规则。

## 重新运行提示并观察变化

1. 返回 Copilot Chat 并选择 **New Chat**，清空对话内容。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 聚焦于正确的文件。
3. 发送与之前相同的提示：

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 注意现在建议的文件开头包含类似以下内容的注释块：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发行商数据访问辅助函数。
    * 提供用于检索数据库中发行商信息的函数。
    */
   ```

5. 注意建议的函数现在包含类似以下内容的 TSDoc 注释：

   ```typescript
   /**
    * 返回所有发行商的 ID 和名称列表。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 一个 Promise，解析为发行商对象数组。
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用于真实代码。下一练习会在此基础上继续构建。

## 提交说明并推送分支

说明文件与其他项目文件一样，也应使用版本控制。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
2. 在终端中运行以下命令，确认说明更新和辅助函数更改均已存在：

   ```bash
   git status
   ```

3. 暂存并提交说明更新及 Copilot 为辅助函数基础所做的所有更改：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他支持性更新（例如测试），也要添加这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支已推送，现在创建拉取请求并关联待办事项中的说明标准议题。Copilot 可以使用已连接到项目的 GitHub 工具查找议题、创建用于关闭该议题的 PR，并合并 PR。

> [!NOTE]
> 这是首次让 Copilot 代表你在 GitHub 上操作。项目模板已配置 GitHub 工具，因此无需另行设置。Copilot 首次使用工具时，VS Code 可能会提示你登录 GitHub。请按提示授权。后续练习会介绍这种连接所用的 Model Context Protocol。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确保智能体选择器中已选中 **Agent**。
3. 请 Copilot 查找与更新说明文件有关的议题，并从当前分支创建 PR，说明其中既有说明更新，也有用于后续筛选工作的 publisher 辅助函数基础：

   ```
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot 开始查找议题并创建 PR。
5. 系统提示你允许 Copilot 代表你执行 GitHub 操作时，请先审查命令，再选择 **Allow**。
6. 创建 PR 后，提示 Copilot 将 PR 合并到 `main` 并在本地切回 `main`、拉取最新代码：

   ```
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. 系统提示你允许 Copilot 执行 GitHub 操作和 shell 命令时，请先审查命令，再选择 **Allow**。

现在，你已借助 GitHub Copilot 创建并合并拉取请求。

## 总结和后续步骤

你已了解 Copilot 如何使用本项目的说明文件，并在 VS Code 中使用 Copilot Chat：

- 发送代码生成提示，观察现有说明下 Copilot 的输出。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次发送相同提示，观察建议的代码如何遵循新标准。
- 将说明更新和辅助函数基础提交到 `main`，供下一练习使用。

接下来，在[代理模式][next-lesson]中让 Copilot 使用这些说明跨代码库添加新功能。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 的个人自定义说明][personal-instructions]
- [Awesome Copilot：说明文件和其他资源合集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
