---
title: "演習 2 - Copilot エージェント モードで新機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの単純な更新でも、通常は複数のファイルを変更し、テストを実行するなどの作業が必要です。開発者は通常、必要なファイルを探し、変更を行い、テストを実行し、問題をデバッグし、変更漏れのファイルを特定して再度更新します。作業は次々と続きます。

こうした場面で Copilot エージェント モードが役立ちます。

Copilot エージェント モードは、IDE 内でより自律的に作業するように設計されています。開発者と同様に、既存のプロジェクト構成を調べ、必要な更新を行い、テストなどのタスクを実行し、見つかった問題を自動的に修正します。エージェント モードでサイトに新機能を追加する方法を確認しましょう。

> [!NOTE]
> 名前は似ていますが、エージェント モードと cloud agent は異なる用途向けに作られています。エージェント モードは IDE 内で動作し、すばやいフィードバックと対話を可能にします。Cloud agent はチームの一員のように非同期で作業し、Issue やプルリクエストを通じてやりとりするペア プログラマーとして設計されています。

この演習では、次のことを学びます。

- Copilot エージェント モードでプロジェクトを調べ、関連ファイルを特定し、複数の変更を調整する方法。
- GitHub Copilot エージェント モードで UI とデータレイヤーにわたる新機能を実装する方法。
- コードベースにマージする前に、Copilot エージェント モードが生成した変更とテストをレビューする方法。

## シナリオ

ゲームの一覧が増えてきたため、ユーザーがカテゴリや発行元で絞り込めるようにします。前の演習では発行元ヘルパーを追加しました。次は Copilot エージェント モードで、残りのデータレイヤー、UI、テストを完成させます。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の現在の機能を確認しましょう。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディング プラットフォームです。ゲームを一覧表示し、その詳細を表示できます。単一の Astro アプリであり、ビルド時にページを静的 HTML としてレンダリングします。ページは frontmatter 内で Drizzle ORM を通じてローカル SQLite データベースを直接クエリします。独立したバックエンド API やクライアント側 UI フレームワークはありません。再利用可能なデータアクセス ヘルパーは `src/lib/` にあり、必要なインタラクティブ機能は標準 DOM API を使う小規模な Astro `<script>` で追加します。

### Web サイトを起動する

Web サイトを簡単に実行できるように、単一 Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で次の手順を実行します。

1. Codespace に戻ります。前の演習の後なので、`main` にいるはずです。
2. Codespace 内で <kbd>Control</kbd>+<kbd>\`</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>\`</kbd>（Windows/Linux）を押して、新しいターミナルを開きます。
3. フィルター機能用の新しいブランチを作成して切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドを実行して Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーが起動すると、次のような URL を含むバナーが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` を開くよう促すダイアログが表示された場合は、**x** を選択して閉じます。

5. ターミナルに表示された Astro サーバーのアドレス `http://localhost:4321` を、<kbd>Command</kbd>（Mac）または <kbd>Ctrl</kbd>（Windows/Linux）を押しながら選択して、Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost の URL を選択すると、自動的に `https://<your-codespace-name>-4321.app.github.dev/` に転送されます。これは Codespace の Web サーバーを公開するプライベート トンネルです。

### Web サイトを確認する

サイトを起動したら、機能を確認できます。主なページは次のとおりです。

- **ホーム ページ**: ボードゲームの一覧と、タイトル、画像、説明を表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、そのタイトル、説明、発行元、カテゴリなどの情報を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードやモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

Web サイトの初期実装は動作しますが、新しい機能で強化したいと考えています。まずバックログを確認しましょう。テンプレートからプロジェクトをセットアップしたときに、Issue のバックログも自動的に作成されています。GitHub Copilot に項目を表示してもらいます。

1. Chat ビューのエージェント選択メニューから **Agent** を選択します。**Agent** は自律的に変更の計画と実装を行い、ターミナル コマンドを実行してツールを呼び出します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルの一覧から **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。ワークショップの作成時に Claude Sonnet 4.5 を使用したため、手順にも記載しています。提案の一貫性を高め、スムーズな体験につなげることを意図しています。ただし、LLM の動作は確定的ではないため、ワークショップの説明とは異なる提案が出る場合があります。これは正常な動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは完了できるはずです。

3. 次のプロンプトを Copilot に送信して、Issue のバックログを確認します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue 一覧を確認します。

Copilot が、ユーザーに最も役立つと考える順に項目を優先付けしたことを確認します。

## 指示ファイルを確認する

エージェントにコードを生成してもらう前に、Copilot にコンテキストを提供する指示ファイルを確認します。Web サイトに機能を追加する方法を説明する[ユーザー インターフェイス（UI）指示][ui-instructions]を使います。

1. Codespace で `.github/instructions/ui.instructions.md` に移動します。
2. 機能追加の進め方に関する全体的なガイダンスを確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティに関する原則。
   - ファイルの種類別の指示へのリンク:
     - Astro
     - Tailwind CSS
     - Drizzle

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルからの相対パスです。指示を再利用したり、複雑な内容を管理しやすい単位に分けたり、例やテンプレートを提示したりできます。

## フィルター機能を実装する

フィルター機能を完成させるには、少なくとも次の 3 つの変更が必要です。

- データレイヤー（`src/lib/`）に残りのフィルター ロジックを追加または調整します。
- フィルターの動作を確認するテストを追加または更新します。
- ゲーム一覧ページにフィルター UI を追加します。

さらに、コードベースにマージする前にテストを実行し、成功させる必要があります。Copilot エージェント モードにこれらのタスクを依頼しましょう。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を始めます。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを追加します。

   ![UI 指示ファイルを選択する例](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント選択メニューで、引き続き **Agent** が選択されていることを確認します。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. モデルが引き続き **Claude Sonnet 4.5** であることを確認します。
5. バックログ内の関連 Issue に基づいて機能を実装するよう、次のプロンプトで Copilot に依頼します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がまずプロジェクトを調べ、機能に関連するファイルを探す様子を確認します。データレイヤーのヘルパー、UI、テストが見つかり、その後ファイルの編集とテストの実行が始まります。

   ![Copilot がプロジェクト ファイルを調べている様子](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトの調査、ファイルの変更、テストの実行などを行います。タスクやコードベースの複雑さによって、数分かかることがあります。作業中、コード エディターに **Keep** と **Undo** ボタンが表示される場合があります。作業が完了すると、すべての変更に **Keep** または **Undo** が表示されます。作業中に選択する必要はありません。

7. Copilot から求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat にテストの実行を確認するダイアログが表示された状態](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に停止したり、一部のテストが失敗したりすることがあります。問題ありません。Copilot はタスクを完了し、エラーが見つからなくなるまでコード生成とテストを繰り返します。

   ![Copilot エージェント モードのチャット セッションの完了例](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは、必ずレビューしてください。

10. Web サイトを実行しているブラウザーに戻り、新機能を試します。
11. 動作を確認し、コードをレビューしたら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

Tailspin Toys の Web サイトに機能を追加するため、GitHub Copilot エージェント モードを使いました。次のことを学びました。

- GitHub Copilot エージェント モードで UI とデータレイヤーにわたる新機能を実装する方法。
- Copilot エージェント モードでプロジェクトを調べ、関連ファイルを特定し、複数の変更を調整する方法。
- コードベースにマージする前に、Copilot エージェント モードが生成した変更とテストをレビューする方法。

次は、[Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### ボーナス演習 — ページ分割を実装する

ゲーム一覧が増えると、ページ分割が必要になります。この演習で学んだことを使い、Copilot にページ分割を実装するよう依頼します。次の点を検討してください。

- 既存の指示ファイルを利用するなど、現在のベスト プラクティスに従います。
- ページサイズをユーザーが選べるようにするか、固定値にするかなど、ページ分割の動作を決めます。
- 希望する実装に必要なガイダンスを Copilot に提示するプロンプトを作成します。
- GitHub Copilot に変更を依頼し、コンテキストを提供しながら反復が必要な場合があります。これは Copilot と作業するときの通常の流れです。

## リソース

- [Copilot の Ask、Edit、Agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェント モード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
