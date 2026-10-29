---
title: "演習 3 - Playwright MCP サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェント モード][previous-lesson] · [次のレッスン: カスタム エージェント →][next-lesson]

エージェント モードでフィルター機能を作成しました。プルリクエストを開く前に、ブラウザーで実際に動作することを確認しましょう。自分でアプリを操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot に実際のブラウザーを操作させて機能をテストします。その後、ブランチを公開して PR を開きます。

この演習では、次のことを行います。

- Model Context Protocol（MCP）とは何か、MCP サーバーが新しいツールで Copilot を拡張する方法を学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot に Playwright MCP を使ってもらい、ブラウザーでフィルター機能を手動テストします。
- ブランチを公開し、フィルター機能のプルリクエストを開きます。

## Model Context Protocol（MCP）とは

エージェント モードは、エディターの外部に接続できるとさらに強力になります。Copilot がそのために使うのが Model Context Protocol（MCP）です。エージェントが外部のツールやサービスと通信するための標準的な方法です。

![エージェント モードがコンテキスト、LLM、ツールと連携する仕組みを示す図。MCP サーバーと VS Code 拡張機能が提供するツールを含む](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol（MCP）](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/)を使うと、AI エージェントが外部のツールやサービスと通信できます。MCP を利用すると、AI エージェントはリアルタイムで外部ツールやサービスと通信し、リソースを使って最新情報にアクセスしたり、ツールを使って代理で操作したりできます。

ツールやリソースへのアクセスは MCP サーバーを通じて行います。MCP サーバーは AI エージェントと外部ツールやサービス（既存の API や NPM パッケージなどのローカル ツール）間の通信を管理します。それぞれの MCP サーバーが、エージェントから利用できる異なるツールやリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API にアクセスできます。AI エージェントで新しいリポジトリを作成したり、既存のリポジトリを更新したり、Issue やプルリクエストを管理したりできます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を提供します。AI エージェントで Web ページに移動したり、フォームに入力したり、ボタンを選択したりできます。

ほかにも、さまざまなツールやリソースにアクセスできる MCP サーバーがあります。GitHub は、エコシステム内の発見とコントリビューションを促進するために [MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティの観点から、MCP サーバーはプロジェクト内のほかの依存関係と同様に扱ってください。利用する前にソースコードをよく確認し、公開元を検証して、セキュリティへの影響を検討してください。信頼できる MCP サーバーのみを利用し、機密リソースや操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースから利用できる MCP サーバーを構成します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` サーバーがすでに構成されていることを確認します。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        }
      }
    }
    ```

この `github` エントリはプロジェクト テンプレートに含まれています。そのため、前の演習で Copilot はバックログの Issue を読み取れました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールするものはありません。VS Code は HTTP 経由で接続し、Copilot が初めてツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次は 2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が操作できるブラウザーを利用できます。これを使って機能をテストします。

1. `.vscode/mcp.json` で `github` と並べて `playwright` エントリを追加し、ファイルが次のようになるようにします。

    ```json
    {
      "servers": {
        "github": {
          "type": "http",
          "url": "https://api.githubcopilot.com/mcp/"
        },
        "playwright": {
          "command": "npx",
          "args": ["@playwright/mcp@latest", "--headless"]
        }
      }
    }
    ```

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` は **ローカル** サーバーです。VS Code は `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、画面に表示されない状態で Playwright がブラウザーを実行します。デスクトップ画面がない Codespace 内では、この指定が必要です。

> [!NOTE]
> Tailspin Toys プロジェクトはエンドツーエンドテストに Playwright をすでに使っているため、必要なブラウザーはインストール済みの場合がほとんどです。後で Copilot がブラウザーが見つからないと報告した場合は、`npx playwright install chromium` を実行するよう依頼して、もう一度試してください。

## サーバーを起動して信頼する

VS Code は MCP サーバーを **必要になったときに起動**します。Copilot がそのサーバーのツールを初めて必要としたときに、VS Code がサーバーを起動し、信頼するか確認します。

1. `.vscode/mcp.json` で `playwright` エントリの上に **Start** 操作が表示されます。選択してサーバーを今すぐ起動し、構成が正しいことを確認します。
2. VS Code からサーバーを信頼するか確認されたら、構成をレビューし、サーバーを起動できるよう信頼することを選択します。

手動での起動は任意です。省略した場合は、次の手順で Copilot がツールを初めて必要としたときに、VS Code がサーバーを起動し、信頼するか確認します。

## フィルター機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。自分でアプリを操作して確認する代わりに、エージェントがページを開き、移動し、フィルターを適用し、結果を読み取って要約します。会話を離れずに機能が期待どおり動作することを確認できる、すばやい方法です。

内部では、Playwright MCP サーバーはスクリーンショットではなくページの[アクセシビリティ ツリー][playwright-mcp-server]を使います。つまり、エージェントは支援技術と同じように、ボタン、リンク、リスト項目などの構造化されたラベル付き要素を判断します。そのため、簡単な機能確認がアクセシビリティの簡易チェックにもなります。

サーバーが接続され、アプリが実行中であることを確認してから、次のプロンプトで作成したフィルター機能を操作するよう Copilot に依頼します。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバーを通じてブラウザーを起動し、各手順を実行して結果を報告します。Issue の受け入れ基準に照らして要約を確認してください。問題があれば、追加で質問するか、プルリクエストを作成する前にコードの修正を依頼します。

> [!NOTE]
> このテストには、アプリが `http://localhost:4321` で実行されている必要があります。開発サーバーを停止した場合は、プロンプトを送る前にもう一度起動してください。Copilot が Playwright MCP サーバーを初めて使うときは、ブラウザーをダウンロードする必要がある場合があります。ブラウザーがないと報告された場合は、`npx playwright install chromium` を実行するよう依頼して、もう一度試してください。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認できたので、チームがレビューできるようプルリクエスト（PR）を開きます。まず `filtering-vscode` ブランチを公開します。

1. Codespace で **Source Control** パネルに移動し、Copilot が行った変更をレビューします。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを使ってコミット メッセージを生成します。

   ![変更内容が表示された Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub コマンド ライン インターフェイス（CLI）など、プルリクエストを作成する方法はいくつかあります。GitHub Copilot を使っているので、PR の作成も依頼しましょう。関連する Issue を見つけ、関連付けた PR を作成できます。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. エージェント選択メニューで **Agent** が選択され、Copilot が GitHub ツールを使えることを確認します。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、Copilot が情報を収集して操作を行うことを許可します。GitHub ツールを初めて使うときに認証を求められた場合は、案内に従って許可します。
5. Copilot が Issue を検索し、該当するものを見つけて PR を作成する様子を確認します。
6. Copilot が生成したリンクを選択してプルリクエストをレビューします。ただし、**まだマージしないでください**。

## まとめと次のステップ

この演習では、次のことを行いました。

- Model Context Protocol（MCP）とは何か、MCP サーバーが新しいツールで Copilot を拡張する方法を学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- 出荷前に、ブラウザーでフィルター機能を手動テストするために Playwright MCP を使いました。
- ブランチを公開し、フィルター機能のプルリクエストを開きました。

MCP サーバーを使って機能を *テスト* しましたが、MCP は Copilot を拡張する方法の一つにすぎません。次は、アクセシビリティ レビューなどに特化したタスクを効率化するため、[カスタム エージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration
