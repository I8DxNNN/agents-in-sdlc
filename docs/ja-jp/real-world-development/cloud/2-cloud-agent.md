---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム指示][previous-lesson] |
|:--|

技術的負債に悩まされていない組織は、ほとんどないでしょう。未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能リクエストなど、さまざまな問題があります。GitHub Copilot cloud agent は、コードの更新や機能の追加などのタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフト PR を作成します。これにより、面倒な作業を任せて開発を加速し、開発者はより大きな課題に集中できます。

Copilot cloud agent を使って、次のことを確認します。

- コード生成環境をカスタマイズします。
- 操作を安全に行う方法を確認します。
- 範囲を明確にした Issue の重要性を学びます。
- Issue を Copilot に割り当てます。

## シナリオ

Tailspin Toys には解決したい技術的負債があります。サイトの初期バージョンを作成した委託先がドキュメントを十分に作成しておらず、実際にはほとんど存在しません。まず、アプリケーション内のすべてのエクスポートされた関数に TSDoc のドキュメントコメントを追加したいと考えています。

また、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属する関連ゲームを表示し、閲覧を続けられるようにします。まだ完成度の高い実装は必要なく、UX の受け入れテストに使えるものを求めています。現在は作業の妨げになっていますが、より優先度の高い Issue もあります。

どちらも優先順位が下がりやすいタスクの例であり、Copilot cloud agent に割り当てるのに適しています。Copilot cloud agent に非同期で処理を任せると、開発者はほかの作業に集中でき、後から戻って作業をレビューし、期待どおりの結果になっているか確認できます。

## GitHub Copilot cloud agent について

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同様にバックグラウンドでタスクを実行できます。[GitHub Issue を Copilot に割り当てる][assign-issue]など、複数の方法で作業を依頼できます。割り当てると、Copilot は進捗を追跡するドラフト プルリクエストを作成し、環境をセットアップしてタスクに取り組み始めます。作業中または完了後に Copilot のセッションを詳しく確認できます。提案された解決策がレビュー可能になると、プルリクエストであなたに通知します。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法はありません。考える作業を省略できるわけではありません。一見簡単そうな操作でも、詳しく調べると複雑なことがあります。そのため、[Copilot cloud agent に依頼するタスクの範囲][cloud-agent-best-practices]を慎重に決めてください。AI ペア プログラマーとして扱い、段階的に作業し、学び、試し、必要に応じて調整します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム指示

これまでの演習では、カスタム指示と Copilot への影響を確認しました。カスタム指示の演習をすでに行っていれば、`.github/copilot-instructions.md` の効果を確認しています。まだの場合は、ここで簡単に確認しましょう。Copilot cloud agent に作業を割り当てる前に、このリポジトリに含まれる指示ファイルを読み取り専用で確認しておくと、後でその影響を確認できます。

GitHub Web UI または起動済みの Codespace で、次のファイルを開きます。

- `.github/copilot-instructions.md` — **Code standards** セクション、特に TypeScript の規約と TSDoc のドキュメントコメントに関する要件を確認します。
- `.github/instructions/unit-tests.instructions.md` — `applyTo` フロントマターを確認します。この指示は `**/*.test.ts` ファイルに適用されます。

次のセクションで `Code lacks documentation` Issue を cloud agent に割り当てるとき、作成されるプルリクエストに TSDoc のドキュメントコメント、TypeScript の規約、コメント ヘッダーが反映されているか確認してください。後の演習で PR をレビューするときにも取り上げます。

## Copilot cloud agent の開発環境をセットアップする

コードの作成には、通常、適切な状態を保つための環境とセットアップ スクリプトが必要です。Copilot にタスクを割り当てる場合も同様です。Copilot はソフトウェア エンジニアのような方法で作業します。

Cloud agent は [GitHub Actions][github-actions] を使って作業環境を構築します。`.github/workflows/copilot-setup-steps.yml` に設定する[専用のセットアップ ワークフロー][setup-workflow]を作成すると、作業開始前の環境をカスタマイズできます。これにより、必要な開発ツールと依存関係を利用できるようになります。このラボの進行と学習に役立つよう、あらかじめ設定されています。単一の Astro アプリ用に、Node.js、プロジェクトの依存関係、エンドツーエンドテスト用のブラウザー バイナリ、マイグレーションとシードが済んだ SQLite データベースを Copilot が利用できます。

```yaml
name: "Copilot Setup Steps"

# Allows you to test the setup steps from your repository's "Actions" tab
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Set the permissions to the lowest permissions possible needed for *your steps*. Copilot will be given its own token for its operations.
    permissions:
      # If you want to clone the repository as part of your setup steps, for example to install dependencies, you'll need the `contents: read` permission. If you don't clone the repository in your setup steps, Copilot will do this for you automatically after the steps complete.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Frontend / app setup - Node.js (the whole app is now Astro + Drizzle/libSQL)
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

      # Migrate + seed the local SQLite database so builds and tests have data.
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub ワークフローと同様の形式ですが、いくつか重要な点があります。

- `copilot-setup-steps` という単一のジョブがあります。このジョブは、Copilot がプルリクエストに取り組む前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーに注目してください。リポジトリの **Actions** タブからワークフローを手動実行できます。Copilot が実行するのを待たずに、正常に実行されるかテストできます。

## ドキュメントを追加する

ドキュメントの重要性は広く認識されていますが、多くのプロジェクトでは情報が古かったり、まったくなかったりします。この種の技術的負債は見過ごされやすく、生産性を低下させ、コードベースの保守や新しい開発者の受け入れを難しくします。Copilot はドキュメントの作成を得意としているため、cloud agent に割り当てるのに適した Issue です。バックグラウンドで必要なドキュメントを作成してもらい、後の演習で結果をレビューします。

1. 新しいブラウザー タブで github.com のリポジトリに移動します。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue ダイアログを開きます。
4. **Blank issue** を選択して新しい Issue を作成します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

    ```plaintext
    Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
    ```

7. **Create** を選択して Issue を作成します。
8. 右側で **Assign to Copilot** を選択し、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

    ![Copilot の割り当て詳細](../../../_images/ex4-assign-copilot-details.png)

10. **Pull Requests** タブを選択します。
11. 新しく作成されたプルリクエスト（PR）を開きます。タイトルは `[WIP]: Code lacks documentation` のようなものです。新しい PR が一覧に表示されない場合は、しばらく待ってからブラウザーを更新してください。
12. 数分後、Copilot が ToDo リストを作成したことを確認します。

> [!NOTE]
> Copilot の ToDo リストが PR に表示されるまで数分かかることがあります。Copilot は前述のワークフローを実行して環境を構築し、プロジェクトを分析して、課題に取り組む方法を検討しています。

13. リストと、Copilot が実行するタスクを確認します。
14. プルリクエストのタイムラインを下にスクロールし、Copilot が Issue に取り組み始めたことを示す更新を確認します。
15. **View session** ボタンを選択します。

    ![Copilot のセッション表示](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新された表示を確認するために、ウィンドウの更新が必要な場合があります。

16. ライブ セッションをスクロールし、Copilot が問題を解決する様子を確認します。コードを調べて状態を理解し、適切な計画を考えるために一時停止し、コードを作成する過程を確認できます。

この作業には数分かかることがあります。Copilot cloud agent の主な目的の一つは、タスクを非同期で実行し、その間にほかの作業に集中できるようにすることです。この機能を活用し、もう一つのタスクも Copilot cloud agent に割り当ててから、アプリに機能を追加するコード作成に取り組みます。

## ゲーム詳細ページに関連ゲームのセクションを追加する

GitHub Copilot cloud agent の大きな利点は、作業を分担できることです。デザイン チームが求めている関連ゲームのセクションは、実装にそれほど時間がかからないかもしれませんが、その時間をほかの作業に使えます。Copilot cloud agent に割り当てましょう。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択して、新しい Issue ダイアログを開きます。
4. **Blank issue** を選択して、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

    ```markdown
    We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
    ```

7. **Create** を選択して Issue を作成します。
8. 右側で **Assign to Copilot** を選択し、割り当てダイアログを開きます。

    ![Issue に Copilot を割り当てる](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Copilot が作業を開始したことを示す 👀 が Issue の最初のコメントに表示されます。

![Issue に Copilot が取り組んでいることを示す目の絵文字](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼に取り組んでいます。Copilot cloud agent はソフトウェア エンジニアと同じように作業するため、常に監視する必要はありません。完了後にレビューすればよいのです。次は、カスタム エージェントの作成と使用を確認します。

## まとめと次のステップ

このレッスンでは [GitHub Copilot cloud agent][copilot-agents] を確認しました。cloud agent に Issue を割り当てると、非同期で作業を進められます。技術的負債の解消、新機能の作成、別のフレームワークへのコード移行などを Copilot に依頼できます。

次の内容を確認しました。

- コード生成環境をカスタマイズする方法。
- 操作を安全に行う方法。
- 範囲を明確にした Issue の重要性。
- Issue を Copilot に割り当てる方法。

cloud agent がバックグラウンドで作業している間に、カスタム エージェントの作成と使用に進みます。[Copilot cloud agent は MCP サーバーも利用でき][cloud-agent-mcp]、前のモジュールで確認したカスタム指示も利用できます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタム エージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
