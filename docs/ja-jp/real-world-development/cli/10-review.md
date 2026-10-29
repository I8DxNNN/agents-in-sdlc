---
title: "レッスン 10 - まとめと次のステップ"
description: "Copilot CLI のワークフロー、2 つのプルリクエスト、再利用可能なカスタマイズ、追加リソースを振り返ります。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

GitHub Copilot CLI を使って、Tailspin Toys の一連のワークフローに取り組みました。次のことを行いました。

- Codespace を準備し、Copilot CLI をインストールし、プロジェクトを確認して、登録済みのフィルター機能の Issue を見つけました。
- 星評価を追加し、転送されたブラウザーで結果をレビューして、最初のプルリクエスト（PR）を手動でマージしました。
- フィルター機能の Issue から始め、Plan モードで方針を決め、Autopilot モードで実装し、Interactive モードでレビューしました。
- カスタム指示でエージェントを導き、既存の `quality-checks` スキルをカスタマイズして、ユニットテスト、lint、型チェックを実行しました。
- Playwright Model Context Protocol（MCP）サーバーを追加し、実際のブラウザーでフィルター機能を確認しました。
- 品質保証（QA）用のカスタム エージェントを作成して選択し、要件、テストカバレッジ、スキルの結果、ブラウザーでの検証結果を評価しました。
- フィルター機能の変更全体をレビューし、フィルター機能の PR に対して Agent Merge を許可しました。
- コンテキスト、モデル、共有、任意のクラウド委任に使うスラッシュコマンドを確認しました。

## リリースしたもの

このワークショップでは、更新済みの `main` からそれぞれ別のブランチを作成し、2 つの PR を作成しました。

1. **星評価:** 既存の `starRating` と、評価がないことを示す明確な表示をゲーム カードに追加します。
2. **フィルター機能と品質ワークフロー:** フィルター機能を実装し、指示を更新して機能に適用し、`quality-checks` のレポートをカスタマイズし、QA プロファイルを作成して、関連するテストを追加します。

フィルター機能の計画から PR の作成まで、同じ会話とブランチを使いました。ワークショップを簡潔にするため、これらの作業を 1 つの PR にまとめています。

## さまざまな検証方法

自動テスト、自分で行うブラウザーの確認、MCP を通じて Copilot が行うブラウザー操作など、複数の方法でコードを確認しました。`quality-checks` スキルはユニットテスト、lint、型チェックを実行し、新しい形式で結果を報告しました。QA はこれらの結果と要件、テストカバレッジのレビューをまとめて PR の準備状況を確認しました。

追加したテストは、実際に存在する不足を補うものである必要があります。QA の実行で新しいテストが不要と判断される場合もあります。マージを許可する前に、コードと検証結果をレビューしてください。変更を加えた場合は、影響を受ける検証結果を更新します。

## ベスト プラクティス

Copilot に与えるコンテキストとツールが、作業の結果に影響します。このワークショップでは、指示を更新し、スキルをカスタマイズし、QA プロファイルを作成し、MCP サーバーを構成しました。これらのカスタマイズを会話間で再利用し、チームのニーズに合わせて調整してください。指示は規約を定め、スキルは繰り返し可能なタスクを説明し、カスタム エージェントは専門的な役割を定義し、MCP サーバーは外部ツールに接続します。エージェントの要約だけでなく、実際の変更とツールの結果を確認してください。

タスクに合った **モードとモデル** を選びます。実装前に方針を検討するときは **Plan**、範囲の限られた変更を細かく確認しながら進めるときは **Interactive**、範囲が明確なタスクを任せるときは **Autopilot** を使います。定型的な編集には高速なモデルを、複雑な作業にはより高性能なモデルを選びます。

インフラストラクチャと同じくらい、コンテキストも重要です。何を、なぜ、どのように作りたいかを明確に伝えることで、結果は大きく変わります。

## さらに確認する

主要なワークフローを学びました。Copilot CLI には、次のような機能もあります。

- `/review` でコード レビュー エージェントに変更を分析してもらいます。
- `/rubber-duck` で問題を説明し、別の視点から意見をもらいます。
- `/fleet` で独立したサブタスクを並行して調整します。
- `/worktree` で別のタスクを独立させます。
- `/delegate` で Copilot cloud agent にタスクを送ります。

## 次のステップ

どのツールでも、上達する一番の方法は使い続けることです。実際のコード、趣味のコード、何年も作ろうと思いながら手を付けていなかった小さなアプリなどで活用してください。チームと学んだことを共有し、ほかの人の経験からも学びましょう。そして、ドキュメントも引き続き確認してください。

ターミナルから Tailspin Toys を拡張する場合は、任意の[Foundry Backer Concierge シリーズ][foundry]に進みます。ほかの環境と比較するには、[VS Code ワークショップ][vscode]、[GitHub Copilot app ワークショップ][app]、または [Copilot cloud agent ワークショップ][cloud]を確認してください。

## リソース

- [GitHub Copilot CLI について][about-cli]
- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI をカスタマイズする][customize-cli]
- [Copilot CLI でプルリクエストを管理する][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests
