---
title: "课程 3 - 智能体模式：Plan 和 Autopilot"
description: "使用 Plan 确认实现方案，使用 Autopilot 根据议题构建筛选功能，并在 Interactive 模式下审查结果。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

我们刚刚为项目添加了一项小功能。更大的更改则需要更完整的流程。GitHub Copilot CLI 可配合组织现有的工作流程，帮助我们以正确方式构建正确功能。本课是多个课程中的第一课，你将遵循常见的智能体驱动开发流程：从议题开始实现新功能，验证代码和功能行为，最后成功合并到项目中。

> [!NOTE]
> 后续课程将继续使用同一对话和分支。通常会针对不同文件类型使用不同分支或 PR，但本研讨会会采用捷径，让你专注于核心概念。

本课将学习如何：

- 根据 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式下定义要求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

继续实现此功能时，你还将更新存储库说明、自定义现有 `quality-checks` 技能、添加 MCP 验证、创建 QA 智能体并创建功能 PR。

## 场景

Tailspin Toys 的目录不断扩大，访问者需要按类别和发行商缩小游戏范围。待办议题描述了该功能，但组合类别等细节需要在编写代码前达成共识。你将使用 Plan 模式确定这些决策，然后授权 Autopilot 实现范围明确的任务。

## 背景

将 AI 编码智能体引入开发流程不会改变基本原则，反而使这些原则更加重要。大多数开发者会遵循类似的流程：

1. 从描述待完成工作的议题开始。
2. 制定实现计划。
3. 编写并审查代码。
4. 运行测试以验证代码。
5. 手动验证新功能。
6. 创建拉取请求 (PR)。
7. 代码通过审查且持续集成流程成功后，合并代码。

> [!NOTE]
> 具体流程因团队和组织而异，但大多数流程都与上述步骤类似。

遵循这种标准方法，可以确保 AI 生成的代码满足要求，并与手动编写的代码一样经过审查。

## 对话模式

**对话模式**控制智能体拥有多大自主权。按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 可在模式之间切换：

- **Interactive**：你与智能体协作。智能体提出更改建议，并等待你的输入后再继续。
- **Plan**：智能体先制定计划，但不能编辑项目文件。
- **Autopilot**：智能体自主工作，编写代码、运行测试并不断迭代，直至完成任务。

先使用 Plan 模式，审查计划，再使用 Autopilot 实现计划。

## 从议题开始

开始筛选工作前，返回 Codespace 并确认存储库和终端已准备就绪。

1. 返回 Codespace。如果已停止，请重新启动。
2. 确认星级评分 PR 已合并。
3. 如果终端尚未打开，按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
4. 更新 `main` 并为筛选工作创建分支：

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. 启动 Copilot CLI：

   ```bash
   copilot --yolo
   ```

6. 按两次 <kbd>Tab</kbd> 打开 **Issues** 选项卡。
7. 按 <kbd>A</kbd> 显示所有议题。
8. 使用方向键选中标题为 **Allow users to filter games by category and publisher** 的议题。
9. 按 <kbd>C</kbd> 将议题添加到提示并返回 **Session** 选项卡。

提示现在会以 `#7`（或类似编号）开头。`#` 可将 GitHub 议题或拉取请求 (PR) 加入上下文。

## 规划筛选功能

在实现功能或执行任务前制定计划，可以先明确采用何种方案。处理复杂任务时，花些时间规划通常很有帮助。现在切换到计划模式并让 Copilot 创建计划。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Plan 模式。确认提示下方显示 **Plan**。
2. 在上一步添加的议题引用后输入以下提示：

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot 会先探索项目，再确定适当方案并编写计划。

3. Copilot 可能会询问筛选行为细节。根据偏好回答，没有标准答案。
4. 计划完成后，按 <kbd>Control</kbd>+<kbd>E</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）展开计划。
5. 上下滚动审查计划。
6. 如果计划中的任何部分不符合你的决定，请让 Copilot 修改。

## 批准 Autopilot

计划已编写并审查，现在开始实现。使用 Autopilot 让 Copilot 根据计划迭代，直到认为任务已完成。

1. 选择 **Accept plan and build on autopilot (recommended)**，或已安装版本中措辞相似的选项。
2. 确认提示下方显示 **Autopilot**。
3. 观察 Copilot 如何依据计划编写代码并运行测试。

> [!NOTE]
> 批准后可能会立即开始实现，因此请先审查计划。如果 Copilot 报告缺少依赖项或端口冲突，请先解决设置问题，再将检查视为已完成。

## 审查并验证实现

代码生成后，与其他代码一样，合并前必须审查。审查代码并运行网站，确认一切正常。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 进入 Interactive 模式。确认模式指示器不再显示 **Plan** 或 **Autopilot**。
2. 输入 `/diff` 并检查筛选功能实现和测试。
3. 根据议题和规划阶段确定的决策审查结果。
4. 审查代码后，按 <kbd>Esc</kbd> 退出差异视图。
5. 查看项目检查结果，并要求 Copilot 解决任何失败。

## 探索新功能

代码看起来没问题，但能否正常运行？像之前一样启动应用，通过 Codespaces 转发端口打开网站。

1. 请求 Copilot 启动应用：

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces 报告端口 `4321` 可用时，选择 **Open in Browser**。
3. 尝试按类别筛选、按发行商筛选，以及计划中确定的组合。
4. 确认重置和无结果时的行为符合议题要求和你的决定。
5. 返回 Codespace 并要求 Copilot 停止它启动的开发服务器。

## 总结和后续步骤

你已使用不同的对话模式来构建和审查功能。本课中，你：

- 根据 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义要求。
- 使用 Autopilot 模式实现新功能。
- 审查了代码。
- 在转发的浏览器中手动验证功能。

接下来通过[使用自定义说明][next-lesson]深入了解代码生成，确保代码遵循书面规范。

## 资源

- [GitHub Copilot CLI 中的 Autopilot][autopilot]
- [Copilot CLI 命令参考][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
