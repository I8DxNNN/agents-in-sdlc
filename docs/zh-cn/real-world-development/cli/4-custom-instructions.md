---
title: "课程 4 - 使用自定义说明指导 Copilot"
description: "探索存储库说明、添加文档标准，并将其应用于筛选代码。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

使用生成式 AI 时，上下文至关重要。如果任务需要以特定方式完成，应确保 Copilot 可以获取相关指导。[说明文件][instruction-files]不仅描述想要什么代码，还描述代码应如何组织。现在你已实现筛选功能，接下来将探索 Copilot 使用的说明、添加文档标准并将其应用于代码。

本课将学习如何：

- 了解智能体如何获取存储库说明和按路径应用的说明文件。
- 更新说明文件以确保遵循编码标准。
- 了解说明文件对代码的影响。

## 场景

Tailspin Toys 有一套开发指南和要求，包括：

- 注释应解释意图和不明显的决策，而非复述代码。
- `db/` 和 `src/lib/` 中导出的函数应使用 TSDoc/JSDoc 说明其用途、参数和返回值；如有可注入的 `db` 参数，也应予以说明。
- 可复用 Astro 组件应记录其 `Props` 接口；相关代码更改时，注释应保持准确。
- 应保留现有格式和 lint 指南。

通过说明文件，可以确保 Copilot 掌握相关信息，并按这些实践完成任务。

## 说明文件

自定义说明可向 Copilot 提供上下文和偏好，帮助它理解你的编码风格和要求。你可以指定编码规范、库以及偏好的注释类型。说明既可以应用于整个存储库，也可以应用于特定文件类型或任务。

有两类说明文件：

- `.github/copilot-instructions.md`：每次针对该存储库的请求都会发送此文件，适合存放适用于大多数请求的项目级信息。
- `.github/instructions/*.instructions.md`：用于为特定语言、文件类型或任务提供指导。

> [!NOTE]
> 不同环境支持的其他说明文件格式有所差异。依赖特定格式前，请查看[自定义说明支持参考][custom-instructions-support]。

## 探索项目中的自定义说明文件

起始项目中已包含一组说明文件。先探索现有文件，再进行更改并观察效果。

1. 返回 Codespace。
2. 在 Codespaces 编辑器（而不是终端）中打开 `.github/copilot-instructions.md`。
3. 浏览文件中的项目简介和编码指导。此说明适用于在此存储库中与 Copilot 的所有交互。
4. 打开 `.github/instructions` 文件夹并浏览其中的文件。这里有 Astro 文件、Drizzle 数据层、测试等说明。
5. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它指定说明适用文件的 glob 模式。
6. 打开 `.github/instructions/drizzle.instructions.md`，查看它指向其他说明文件和项目现有文件的链接。这样可以将较大的说明拆分为较小、可复用的文件，并提供示例供 Copilot 参考。

## 更新说明文件以符合团队指南

现有文件已是很好的起点，但仍有缺失。现在修改核心 `copilot-instructions.md` 文件，确保新生成的 TypeScript 代码包含 TSDoc 注释。

1. 在 `.github/copilot-instructions.md` 中找到 **Code formatting guidance** 部分，约在第 35 行。
2. 在该部分末尾添加以下项目符号：

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

文件会自动保存。

## 使用更新后的指导

Copilot CLI 会在启动对话时加载存储库说明。保存更新后的说明后，退出并恢复筛选对话，让 Copilot 在保留功能上下文的同时加载新指导。

1. 如果筛选对话仍处于打开状态，输入 `/exit` 关闭。Copilot CLI 会自动保存对话。
2. 在存储库根目录的 `game-filters-cli` 分支中恢复已保存的对话：

   ```bash
   copilot --resume
   ```

   如果系统提示，请选择筛选对话，并确认当前检出的是 `game-filters-cli` 分支。
3. 让 Copilot 应用更新后的指导：

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. 输入 `/diff` 并查看已更改的 TypeScript 文件。注意新生成的 TSDoc 注释，并确认其准确说明了代码。

## 总结和后续步骤

你已了解 Copilot CLI 如何获取说明文件中的上下文，并将新的标准应用于功能代码。具体来说，你：

- 探索了智能体如何获取存储库说明和按路径应用的说明文件。
- 更新了说明文件以确保遵循编码标准。
- 观察了说明文件对代码的影响。

接下来[自定义并运行可复用的 quality-checks 技能][next-lesson]，确保一致地运行 lint 和测试。

## 资源

- [为 Copilot CLI 添加自定义说明][instruction-files]
- [自定义说明支持][custom-instructions-support]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [Awesome Copilot：说明文件合集][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/
