---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で方針を合意し、Autopilot で Issue に基づくフィルターを実装し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

まずは小さな機能をプロジェクトに追加しました。より大きな変更には、さらにしっかりしたプロセスが必要です。GitHub Copilot CLI は組織の既存の開発フローに沿って動作するため、適切な方法で必要な機能を構築できます。このレッスンから、エージェントを使った一般的な開発プロセスをいくつかのレッスンにわたって実践します。Issue から機能を実装し、コードの妥当性と機能の動作を確認して、最終的にプロジェクトへマージします。

> [!NOTE]
> この機能のワークフローでは、同じ会話とブランチを続けて使います。通常はファイルの種類ごとに異なるブランチや PR を使いますが、ここでは主な概念に集中できるよう簡略化しています。

このレッスンでは、次のことを行います。

- GitHub Issue から Copilot の新しい会話を開始します。
- Plan モードで要件を定義します。
- Autopilot モードで新機能を実装します。
- コードをレビューします。
- 転送されたブラウザーで機能を手動で検証します。

この機能の開発を続けながら、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP による検証を追加して QA エージェントを作成し、機能 PR を開きます。

## シナリオ

Tailspin Toys のカタログは拡大しており、訪問者はカテゴリや発行元でゲームを絞り込む必要があります。バックログの Issue には機能が記載されていますが、カテゴリを組み合わせる方法など、コーディング前に合意が必要な詳細があります。Plan モードでこれらを決めてから、範囲を明確にした実装を Autopilot に許可します。

## 背景

AI コーディングエージェントを開発フローに導入しても、基本は変わりません。むしろ、基本がいっそう重要になります。多くの開発者は、次のような流れで作業します。

1. 実装する内容を記述した Issue から始めます。
2. 構築するものの計画を立てます。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新機能を手動で検証します。
6. プルリクエスト（PR）を作成します。
7. コードレビューと継続的インテグレーションが成功したら、コードをマージします。

> [!NOTE]
> 詳細はチームや組織によって異なりますが、一般的には上記の流れを調整したものになります。

この標準的な手順に従うことで、AI が生成したコードが要件を満たし、手作業で書いたコードと同じレビューと検証を受けるようにできます。

## 会話モード

**会話モード**は、エージェントに与える自律性の度合いを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: エージェントが変更を提案し、続行する前にあなたの入力を待ちます。
- **Plan**: エージェントが先に計画を作成します。プロジェクトファイルの編集はできません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行し、タスクが完了するまで反復します。

まず Plan モードで計画をレビューしてから、Autopilot で実装します。

## Issue から始める

フィルター機能の作業を始める前に、Codespace に戻り、リポジトリとターミナルの準備ができていることを確認します。

1. Codespace に戻ります。停止している場合は、再起動してから続けます。
2. 星評価の PR がマージされていることを確認します。
3. ターミナルが開いていない場合は、<kbd>Control</kbd>+<kbd>\`</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）を押します。
4. `main` を更新し、フィルター機能用のブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの Issue を選択します。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトが `#7`（または同様の番号）から始まっていることを確認します。`#` を使うと、GitHub 上の Issue やプルリクエスト（PR）をコンテキストに追加できます。

## フィルター機能を計画する

計画を立てると、Copilot に機能やタスクを任せる前に、実装方法を決められます。複雑な作業では、時間をかけて計画することをお勧めします。Plan モードに切り替えて、Copilot に計画を作成してもらいましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下にあるモード表示が **Plan** になっていることを確認します。
2. 前の手順で追加した Issue 参照の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べ、適切な方法を検討して計画を作成します。

3. フィルターの動作について質問された場合は、希望に応じて回答します。正解は一つではありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）を押して計画を展開します。
5. 計画を上下にスクロールしてレビューします。
6. 自分の決定と合っていない部分があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画を作成してレビューしたら、実装に進みます。Autopilot モードで Copilot に作業を任せましょう。

Autopilot を使うと、Copilot はタスクが完了したと判断するまで問題に取り組み、反復します。

1. **Accept plan and build on autopilot (recommended)**、またはインストール済みのバージョンで同様のラベルの選択肢を選びます。
2. プロンプトの下にあるモード表示が **Autopilot** になっていることを確認します。
3. Copilot が計画に沿って反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認すると、すぐに実装が始まる場合があります。先に計画をレビューしてください。Copilot が依存関係の不足やポートの競合を報告した場合は、チェック完了と見なす前にセットアップの問題を解決します。

## 実装をレビューして検証する

ほかのコードと同様に、生成されたコードもマージ前にレビューします。コードをレビューし、サイトを起動して問題がないことを確認しましょう。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードにします。モード表示に **Plan** または **Autopilot** が表示されていないことを確認します。
2. `/diff` を実行し、フィルターの実装とテストを確認します。
3. Issue の要件と計画中に決めた内容に照らし合わせます。
4. コードをレビューしたら、<kbd>Esc</kbd> を押して差分表示を終了します。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に解決を依頼します。

## 新機能を試す

コードに問題がないことを確認できました。実際に動作するか試しましょう。前と同じようにアプリを起動し、Codespaces の転送ポートからサイトを開きます。

1. 次のプロンプトで Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces でポート `4321` が利用可能になったら、**Open in Browser** を選択します。
3. 計画で合意したカテゴリ、発行元、およびその組み合わせで絞り込みます。
4. リセットと結果が空の場合の動作が、Issue と決定内容に合っていることを確認します。
5. Codespace に戻り、起動した開発サーバーを停止するよう Copilot に依頼します。

## まとめと次のステップ

異なる会話モードを使って機能を構築し、レビューしました。このレッスンでは、次のことを行いました。

- GitHub Issue から Copilot の新しい会話を開始しました。
- Plan モードで要件を定義しました。
- Autopilot モードで新機能を実装しました。
- コードをレビューしました。
- 転送されたブラウザーで機能を手動で検証しました。

次は、[カスタム指示を使って][next-lesson]、コードが文書化されたプラクティスに沿うようにします。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンド リファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/
