---
title: "演習 1 - カスタム指示 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェント モード →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot に知っておいてほしい背景情報がある場合は、そのコンテキストを参照できるようにします。特定のコンテキストを Copilot と共有する方法はいくつかあります。中でも重要なのは[指示ファイル][instruction-files]です。指示ファイルを使うと、コード生成に関するガイダンスを提供できます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメントの規約が Copilot に伝わる方法を確認します。
- 現在の指示を使って、フィルター機能の最初のデータ部分（発行元ヘルパー）を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する新しい規約を追加します。
- 同じプロンプトをもう一度実行し、生成コードに新しい規約が反映されることを確認します。
- 次の演習で Copilot が更新済みのガイダンスを使えるよう、指示の更新とフィルター機能の基盤を `main` にコミットします。

> [!CAUTION]
> 生成コードが設定した規約の一部に従わない場合があります。Copilot の動作は確定的ではありません。この演習では、出力を完全に一致させるのではなく、指示を更新した後に動作がどう変化するかを確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には、次のとおりです。

- データレイヤーには必ずユニットテストを用意します。
- UI はダークモードにし、現代的な見た目にします。
- TSDoc のドキュメントコメントをコードに追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメント ブロックを追加します。

指示ファイルを使って、Copilot がこれらのプラクティスに沿ってタスクを実行するための情報を提供します。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを生成できるようにします。リポジトリ全体、または特定のファイルやタスクに対するコーディング規約、ライブラリ、コメントの書き方などを指定できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する **すべての** リクエストで Copilot に送信される単一の指示ファイルです。技術スタック、開発対象の概要、ベスト プラクティス、全般的なガイダンスなど、Copilot に対するチャットや CLI のリクエストの大半に関係するプロジェクトレベルの情報を記載します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に関するガイドラインを記載できます。TypeScript や Astro などの言語、UI コンポーネントの作成、ユニットテストの追加などに関する指示を指定できます。

> [!NOTE]
> IDE で作業する場合、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、リポジトリレベルの指示と `applyTo` フロントマターで対象を指定した `*.instructions.md` ファイルの両方を使ってコードを生成します。
>
> Copilot は、AGENTS.md や CLAUDE.md など、ほかの規格に基づく指示ファイルにも対応しています。[対応する指示ファイル][custom-instructions-support]を参照してください。

### 指示ファイルを管理するためのベスト プラクティス

指示ファイルの作成方法を詳しく説明することは、このワークショップの範囲外です。ここでは、サンプル プロジェクトに含まれるファイルを使って代表的な方法を紹介します。基本的には、次の点を意識してください。

- `copilot-instructions.md` には、開発対象、プロジェクトの構成、全体的なコーディング規約など、プロジェクトレベルのガイダンスを記載します。
- `*.instructions.md` には、ファイルの種類（ユニットテスト、Astro コンポーネント、データレイヤーなど）や特定のタスクに関する指示を記載します。
- 自然な言葉を使い、明確なガイダンスを記載します。望ましいコードと避けるべきコードの例も示します。

指示ファイルの作成方法は一つではありません。AI の使い方と同様に、プロジェクトで試しながら最適な方法を見つけてください。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトの指示ファイルを確認すると、[UI の更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク用のファイルが見つかります。
>
> Copilot に指示ファイルを生成してもらうこともできます。VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` など、利用する環境によって方法が異なります。各環境のレッスンで、関連する場合に説明します。
>
> テンプレートやひな形を探している場合は、指示ファイル、カスタム エージェントなどを集めた [awesome-copilot][awesome-copilot] を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## このプロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを確認しましょう。中核となる `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開きます。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には **GitHub Actions Workflows** に関するガイダンスもあります。これらの指示は、Copilot とのあらゆるやりとりに適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データレイヤー、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。これはリポジトリのルートからの相対 glob で、指示の適用対象を指定します。たとえば、`**/*.test.ts` に一致する TypeScript テストファイルが対象になります。
5. このプロジェクトでユニットテストを作成する際の指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの指示ファイルや、プロジェクト内の既存ファイルへのリンクがあります。大きな指示を再利用可能な小さなファイルに分け、コード生成の参考例を Copilot に示せます（ここにあるパスはリポジトリのルートではなく、指示ファイルからの相対パスです）。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング規約が記載されていますが、コード内のドキュメントに関する要件はまだありません。次の手順で、TSDoc のドキュメントコメントとファイルコメント ヘッダーの規則を追加します。

## 変更用のブランチを作成する

ベスト プラクティスに沿って、変更用のブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>`</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）を押して、新しいターミナルを開きます。
3. 新しいブランチを作成して切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 指示を更新する *前* に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を始めます。モードやモデルの選択は演習によって異なります。必要な場合は、各手順で説明します。

カスタム指示の影響を確認するため、まず現在の指示ファイルを使ってプロンプトを送信します。その後、指示を更新し、同じプロンプトを再送信して違いを確認します。

1. 前の演習のエディター タブを閉じ、Copilot が必要なコンテキストだけを取得するようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置場所を Copilot に示します。
3. Chat ビューのエージェント ドロップダウンから **Agent** を選択し、Copilot がファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニュー](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot はプロジェクトを調べ、コードを更新します。多くの場合、ヘルパー ファイルとテストの両方が変更されます。
6. 提案されたヘルパーは、最初の引数として `db` クライアントを受け取り、発行元の型付き配列を返す型付き関数です。これは `.github/instructions/drizzle.instructions.md` のデータレイヤー規約（`src/lib/*.ts` に適用）によるものです。
7. 提案されたコードには、TSDoc のドキュメントコメントとファイルレベルのコメント ヘッダーが **ない** ことを確認します。

> [!CAUTION]
> Copilot の動作は確定的ではないため、指示がなくてもドキュメントコメントが追加されることがあります。その場合も問題ありません。ここでは、指示の更新後に一貫性が改善されることを確認します。

## リポジトリ全体に適用する規約を追加する

前述のとおり、`.github/copilot-instructions.md` は Copilot にプロジェクトレベルの情報を提供します。コード提案を改善するために、リポジトリのコーディング規約を明記しましょう。

1. `.github/copilot-instructions.md` を再度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。既存のコーディング規約が記載されていますが、コード内のドキュメントに関する規則はまだありません。そのため、生成されたヘルパーにはドキュメントコメントがありませんでした。
3. 既存の規約のすぐ下に次の Markdown を追加し、ファイルコメント ヘッダーと TSDoc のドキュメントコメントを追加するよう Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ全体に適用する指示は `.github/copilot-instructions.md` に、言語、ファイルの種類、タスクに固有の指示は `*.instructions.md` に記載できます。今回追加したドキュメントコメントの規則のような、プロジェクト全体の標準はリポジトリレベルのファイルに記載します。

## プロンプトを再実行して変化を確認する

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` をもう一度選択し、Copilot が適切なファイルに注目するようにします。
3. 先ほどと**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案されたファイルの先頭に、次のようなコメント ブロックが追加されていることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

新しいプロジェクト規約に従うよう Copilot を導き、次の演習で使用する実際のコードに適用できました。

## 指示をコミットしてブランチをプッシュする

指示ファイルもリポジトリ内のほかの資産と同じように、ソース管理で管理します。変更をコミットしてブランチをリポジトリにプッシュしましょう。

1. <kbd>Control</kbd>+<kbd>`</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）を押し、新しいターミナルを開きます。
2. 次のコマンドを実行し、指示とヘルパーの変更があることを確認します。

   ```bash
   git status
   ```

3. ターミナルから、指示の更新とヘルパー基盤のために Copilot が変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # If git status shows additional supporting updates (for example tests), add those files too.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. ブランチをリポジトリにプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログのドキュメント規約に関する Issue に関連付けてプルリクエストを作成します。手動で行うこともできますが、プロジェクトに接続済みの GitHub ツールを使って Copilot に依頼できます。Issue を検索し、Issue をクローズする PR を作成してから、マージを依頼しましょう。

> [!NOTE]
> Copilot が GitHub 上で操作するのは、この演習が初めてです。プロジェクト テンプレートには GitHub ツールがあらかじめ接続されています。そのため、設定は不要ですが、Copilot が初めてツールを使うときに VS Code から GitHub へのサインインを求められることがあります。案内に従って許可してください。後の演習で、Model Context Protocol を使った接続方法を学びます。

1. Codespace 内で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）を押して Copilot Chat ビューを開き、エージェント選択メニューで **Agent** が選択されていることを確認します。
3. 指示ファイル更新に関する Issue を探し、現在のブランチから PR を作成するよう Copilot に依頼します。指示の更新と発行元ヘルパーの基盤の両方を説明に含めます。

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が Issue を検索し、PR を作成します。
5. Copilot が GitHub の操作を実行する許可を求めたら、コマンドを確認し、問題がなければ **Allow** を選択します。
6. PR が作成されたら、次のプロンプトで PR のマージとブランチの `main` への切り替えを依頼します。

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. Copilot が GitHub の操作とシェル コマンドを実行する許可を求めたら、コマンドを確認し、問題がなければ **Allow** を選択します。

GitHub Copilot の支援を受けてプルリクエストを作成し、マージできました。

## まとめと次のステップ

Copilot がこのプロジェクトの指示を参照する方法を確認し、VS Code の Copilot Chat を使って、次のことを行いました。

- 既存の指示を適用したコード生成プロンプトを送り、Copilot の出力を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する新しい規約を追加しました。
- 同じプロンプトを再度実行し、提案されたコードに新しい規約が反映されることを確認しました。
- 指示の更新とヘルパー基盤を `main` にコミットし、次の演習で使用できるようにしました。

次は、Copilot がコードベース全体に新機能を追加するときに、[エージェント モード][next-lesson]で指示を活用します。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示のベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム指示][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
