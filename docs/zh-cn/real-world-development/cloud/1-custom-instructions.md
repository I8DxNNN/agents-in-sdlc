---
title: "练习 1 - 自定义说明（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，或 Copilot 需要了解背景信息，就要确保它能访问这些上下文。[说明文件][instruction-files]可为 Copilot 提供指导，让它了解你希望完成什么，以及应该如何完成。

本练习将学习如何：

- 通过存储库自定义说明和按路径应用的说明文件，将项目上下文、编码指南和文档规范提供给 Copilot。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 学习路径不同，本练习不会比较更新说明前后的提示结果。Copilot 云智能体会异步处理 GitHub 议题，因此难以实时并排展示影响。稍后审查云智能体创建的拉取请求时，你会看到说明文件的作用。

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

## 添加存储库级标准

接下来要编辑的内容只有一项：添加项目级规则，要求在代码中使用 TSDoc 文档注释和文件级注释头。云智能体稍后处理本学习路径中的议题时会遵循这些规则。

编辑前，先创建分支（云智能体会读取议题目标分支中的说明，但仍应单独提交这项更改）：

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

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

## 提交、推送并合并说明更新

云智能体会读取议题目标分支中的说明。下一课将从 `main` 分支为 Copilot 分配议题，因此必须先将这些说明合并到 `main`，云智能体才能使用。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 合并到 `main` 的拉取请求并将其合并。下一课中分配议题后，云智能体就会读取这些说明。

> [!TIP]
> 如果希望在本研讨会中直接使用 `main` 分支，可以跳过创建分支的步骤并直接提交到 `main`。此处创建分支是为了模拟真实项目中的工作方式。

## 总结和后续步骤

你已了解 Copilot 如何使用本项目的说明文件，并在 `.github/copilot-instructions.md` 中添加了新的存储库级标准。接下来，云智能体创建拉取请求时，你会看到这项标准的实际效果。

继续[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [用于自定义 GitHub Copilot 的说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更好的 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot：说明文件和其他资源合集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
