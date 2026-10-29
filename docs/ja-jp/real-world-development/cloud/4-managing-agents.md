---
title: "演習 4 - エージェントを監視して管理する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

直前の演習では、ユーザー体験の改善と機能追加を目的に、Copilot cloud agent に 3 つのタスクを依頼しました。cloud agent は非同期かつ自律的に作業しますが、タスクを監視することも重要です。

cloud agent に割り当てたタスクを管理する方法はいくつかあります。GitHub.com の[エージェント ページ][agents-page]もその一つです。この mission control では、オープンなプルリクエスト（PR）があるすべてのエージェント タスクを確認できます。実行された操作を調べたり、進行中のセッションに追加の指示を与えて作業を導いたりできます。

このレッスンでは、次のことを行います。

- エージェント ページで cloud agent のタスクを監視します。
- 進行中のセッションに追加の機能を依頼します。

## シナリオ

エージェントにハイ コントラスト モードの作成を依頼した後、チームはライト モードも追加するよい機会だと考えました。サイトのスタイル変更と切り替え機能の実装をすでに進めているため、ライト モードも追加するのが自然です。エージェントに指示を追加し、ハイ コントラストに加えてライト モードも実装してもらいます。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの現在の状況を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)に移動します。
2. メイン ペインと左ペインの両方に表示されるタスク一覧を確認します。次のようなタスクが表示されます。
   - コードベースのドキュメントの更新。
   - ゲーム変更用 API の生成。
   - Web サイトへのハイ コントラスト モード追加。
3. 実行中のタスクを選択し、Copilot が実行した操作を確認します。次のような操作が含まれます。
   - リポジトリのチェックアウト。
   - Copilot が作業する環境の作成。
   - MCP サーバーのセットアップ。
   - 割り当てられたタスクを完了するための各種手順。

> [!NOTE]
> 表示される手順は、Copilot の作業状況や採用した方法によって異なります。

4. 右側に表示されるプルリクエスト（PR）ペインも確認します。ここから PR と変更されたファイルを確認し、さらに詳しく監視できます。

## Cloud agent に指示を追加する

実行中のタスクを確認できました。次は、ハイ コントラスト モードの作業中にライト モードの切り替えも追加するよう Copilot に依頼します。

1. ハイ コントラスト モードを追加するセッションを選択します。正確なタイトルは、Copilot が付けた名前や作業状況によって異なります。

    ![mission control のアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了して作業が始まったことを示すメッセージが表示されるまで、数分間セッションを確認します。
3. **Steer active session while Copilot is working** ダイアログに次のプロンプトを入力します。

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Copilot の作業中に指示を追加するダイアログが強調表示された、agents ページの cloud agent タスクのスクリーンショット](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業に取り込んだことを確認します。

## Copilot の作業を待つ

これまでと同様に、Copilot は更新されたタスクに取り組みます。メッセージを送信した時点で作業していた手順が終わると、新しい依頼をワークフローに取り込みます。

今回も数分かかります。その間に、これまでに学んだことを振り返りましょう。

## まとめと次のステップ

このレッスンでは、GitHub Copilot cloud agent のタスクを監視して導くための中心的な場所である、Copilot agents ページを確認しました。この mission control を使うと、進行中と完了済みのタスクを追跡し、作業内容をレビューし、進行中のタスクの範囲を変更したり追加の指示を与えたりできます。

次の内容を確認しました。

- mission control とエージェント ページで cloud agent のタスクを監視しました。
- 進行中のセッションに指示を追加し、機能を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、Copilot が作成したプルリクエストを[反復的に改善する][next-lesson]方法を確認しましょう。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタム エージェントについて][custom-agents]

---

| [← 前のレッスン: カスタム エージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
