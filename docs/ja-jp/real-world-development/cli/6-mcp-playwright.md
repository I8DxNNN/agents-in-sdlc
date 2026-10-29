---
title: "レッスン 6 - Playwright MCP で機能を検証する"
description: "Copilot CLI で Playwright MCP を確認または構成し、ブラウザーを使ってフィルター機能を調べます。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

これまでに説明したように、コードを書くこと以外にも多くの作業があります。データや外部サービスを扱い、Copilot が利用できる自動化機能を追加する必要もあります。そこで MCP サーバーを使います。MCP サーバーを使うと、Copilot CLI に組み込まれている機能を超えて、さらに多くのツールやサービスを利用できます。

このレッスンでは、次のことを行います。

- Model Context Protocol（MCP）とは何か、Copilot CLI がどのように利用するかを理解します。
- Playwright MCP サーバーがまだ利用できない場合は追加します。
- エージェントにブラウザーを操作させ、フィルター機能を調べます。

## シナリオ

ユニットテストとエンドツーエンドテストは重要ですが、UI の更新を検証するには、実際に操作する必要があります。ユーザーと同じように作業中の Web サイトを Copilot が利用できるようにし、変更を行う手順をさらに自動化して、期待どおりに動作する確信を高めます。

## Model Context Protocol（MCP）とは

[Model Context Protocol（MCP）][mcp-blog-post]を使うと、AI エージェントが外部のツールやサービスと通信できます。MCP を利用すると、AI エージェントはリアルタイムで外部ツールやサービスにアクセスし、最新の情報を取得したり、代理で操作したりできます。

ツールやリソースへのアクセスは MCP サーバーを通じて行います。MCP サーバーは AI エージェントと外部ツールやサービスをつなぐ役割を担います。それぞれの MCP サーバーが、エージェントから利用できる異なるツールやリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server][github-mcp]**: GitHub リポジトリ、Issue、プルリクエストを管理する API にアクセスできます。
- **[Playwright MCP Server][playwright-mcp-server]**: Playwright を使ったブラウザー自動化機能を提供します。

ほかにも多くの MCP サーバーがあります。GitHub は、エコシステム内の発見とコントリビューションを促進するために [MCP レジストリ][mcp-registry]をホストしています。

> [!CAUTION]
> MCP サーバーは、ほかの依存関係と同様に扱ってください。利用する前にソースコードを確認し、公開元を検証して、セキュリティへの影響を検討してください。

## Playwright MCP サーバーを追加する

Playwright MCP サーバーがまだ利用できない場合は、Copilot がユーザーと同じように Web サイトを操作できるよう追加します。

> [!NOTE]
> Playwright MCP が Copilot CLI ですでに構成されている場合は、これらのセットアップ手順を省略して次のセクションに進みます。

1. Codespace に戻ります。
2. Copilot CLI で次のコマンドを実行し、MCP サーバー追加ダイアログを開きます。

   ```plaintext
   /mcp add
   ```

3. 名前に `playwright` と入力し、<kbd>Tab</kbd> を押します。
4. <kbd>Enter</kbd> を押してサーバーの種類が **STDIO** であることを確認し、<kbd>Tab</kbd> を押します。
5. **Command** ダイアログに次の内容を貼り付けます。

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. <kbd>Control</kbd>+<kbd>S</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）を押して、新しい MCP サーバーを保存します。
7. <kbd>Esc</kbd> を押して MCP ダイアログを閉じます。

## Playwright で機能を調べるよう Copilot に依頼する

これまでは、機能が期待どおり動作することを手動で確認しました。今度は Copilot で利用できる Playwright MCP サーバーを使って、同じ確認を行います。

1. 次のプロンプトで、Playwright MCP サーバーを使って機能を検証するよう Copilot に依頼します。

   ```plaintext
   Start the app and use Playwright MCP to check filtering against the issue and our plan. Tell me what works and what doesn't, without making changes. Stop the server you started when you're done.
   ```

> [!NOTE]
> 通常は MCP サーバーを使うよう Copilot に明示しなくても、自動的に判断されます。ただし、使用するツールがわかっている場合は、明示しても問題ありません。結果が安定しやすくなり、トークンも節約できます。

2. 機能の動作を確認するために、Copilot がブラウザーで実行する手順を確認します。
3. レポートを読み、すべて期待どおりに動作していることを確認します。

Copilot はサーバーを起動し、Playwright で Web サイトを操作してからサーバーを停止し、結果をレポートします。

## まとめと次のステップ

Copilot CLI から Playwright MCP サーバーを使い、実際のブラウザーで機能を確認できました。このレッスンでは、次のことを行いました。

- Model Context Protocol（MCP）とは何か、Copilot CLI がどのように利用するかを学びました。
- Playwright MCP サーバーがまだ利用できない場合に追加しました。
- エージェントにブラウザーを操作させ、フィルター機能を調べました。

次は、スキルとブラウザーツールを専門的な役割として組み合わせる [QA カスタム エージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ話題になっているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [Copilot CLI に MCP サーバーを追加する][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers
