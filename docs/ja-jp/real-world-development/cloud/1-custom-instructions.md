---
title: "演習 1 - カスタム指示 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot に知っておいてほしい背景情報がある場合は、そのコンテキストを参照できるようにします。[指示ファイル][instruction-files]を使うと、Copilot に実行内容だけでなく方法も伝えられます。

この演習では、次のことを行います。

- リポジトリのカスタム指示とパスで対象を限定した指示ファイルを通じて、プロジェクト固有のコンテキスト、コーディングガイドライン、ドキュメントの規約が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` にリポジトリ全体に適用する新しい規約を追加します。

> [!NOTE]
> VS Code および CLI のハーネスと異なり、この演習では変更前後のプロンプトを比較しません。Copilot cloud agent は GitHub Issue に対して非同期で動作するため、リアルタイムで影響を比較するのは困難です。後の演習で cloud agent が作成したプルリクエストをレビューするときに、指示ファイルの影響を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発手法に関するガイドラインと要件があります。具体的には、次のとおりです。

- データレイヤーには必ずユニットテストを用意します。
- UI はダークモードにし、現代的な見た目にします。
- TSDoc のドキュメントコメントをコードに追加します。
- 各ファイルの先頭に、ファイルの役割を説明するコメントブロックを追加します。

指示ファイルを使って、Copilot がこれらのプラクティスに沿ってタスクを実行するための情報を提供します。

### カスタム指示

カスタム指示を使うと、コーディングスタイルや要件を Copilot に伝え、より適切な提案やコードを生成できるようにします。リポジトリ全体、または特定のファイルやタスクに対する規約、ライブラリ、コメントの書き方などを指定できます。

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

指示ファイルの作成方法は一つではありません。AI の使い方と同様に、プロジェクトで試しながら、最適な方法を見つけてください。

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

## プロジェクトのカスタム指示ファイルを確認する

このリポジトリに含まれる指示ファイルを確認しましょう。中核となる `copilot-instructions.md` と、タスク別の `*.instructions.md` ファイルがあります。エディターまたは GitHub Web UI で開きます。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの簡単な説明と、**Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** の下には、**GitHub Actions Workflows** に関するガイダンスもあります。これらの指示は、Copilot とのあらゆるやりとりに適用されます。
3. `.github/instructions` フォルダーを開いて内容を確認します。Astro ファイル、Drizzle データレイヤー、テストなどに関する指示があります。
4. `.github/instructions/unit-tests.instructions.md` を開き、先頭の `applyTo` フィールドを確認します。これはリポジトリのルートからの相対 glob で、指示の適用対象を指定します。たとえば、`**/*.test.ts` に一致する TypeScript テストファイルが対象になります。
5. このプロジェクトでユニットテストを作成する際の指示を確認します。
6. 最後に `.github/instructions/drizzle.instructions.md` を開き、末尾までスクロールします。`unit-tests.instructions.md` などの指示ファイルや、プロジェクト内の既存ファイルへのリンクがあります。大きな指示を再利用可能な小さなファイルに分け、コード生成の参考例を Copilot に示せます（ここにあるパスはリポジトリのルートではなく、指示ファイルからの相対パスです）。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはプロジェクトのコーディング規約が記載されていますが、コード内のドキュメントに関する要件はまだありません。次の手順で、TSDoc のドキュメントコメントとファイルコメント ヘッダーの規則を追加します。

## リポジトリ全体に適用する規約を追加する

この演習で編集するのは、プロジェクト全体に適用する規則です。コードに TSDoc のドキュメントコメントとファイルコメント ヘッダーを追加するようにします。cloud agent は、このハーネスの後半で割り当てる Issue に取り組む際に、この規則を参照します。

編集の前に、作業用ブランチを作成して切り替えます（cloud agent は Issue の対象ブランチから指示を読み込みますが、ここでは変更をきれいにコミットできるようにします）。

1. Codespace のターミナルで、次のコマンドを実行してブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

前述のとおり、`.github/copilot-instructions.md` は Copilot にプロジェクトレベルの情報を提供します。コード提案を改善するために、リポジトリのコーディング規約を明記しましょう。

1. `.github/copilot-instructions.md` を再度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。既存のコーディング規約が記載されていますが、生成されたヘルパーにドキュメントコメントがなかったのは、コード内のドキュメントに関する規則がまだないためです。
3. 既存の規約のすぐ下に次の Markdown を追加し、ファイルコメント ヘッダーと TSDoc のドキュメントコメントを追加するよう Copilot に指示します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 前のレッスンで確認したように、リポジトリ全体に適用する指示は `.github/copilot-instructions.md` に、言語、ファイルの種類、タスクに固有の指示は `*.instructions.md` に記載できます。今回追加したドキュメントコメントの規則のような、プロジェクト全体の規約はリポジトリレベルのファイルに記載します。

## 指示の変更をコミット、プッシュ、マージする

Cloud agent は、Issue が対象としているブランチから指示ファイルを読み込みます。次の演習で Issue を Copilot に割り当てるとき、Copilot は `main` からブランチを作成します。そのため、cloud agent が指示を参照できるよう、変更を `main` に反映する必要があります。

1. ステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` に向けたプルリクエストを開き、マージします。次の演習で Issue に取り組む際に、cloud agent はこの指示を読み込みます。

> [!TIP]
> このワークショップで `main` に直接作業したい場合は、ブランチを作成せずに `main` にコミットしてもかまいません。ここでは実際のプロジェクトでの手順に近づけるため、ブランチを使っています。

## まとめと次のステップ

Copilot が指示ファイルを参照する方法を確認し、`.github/copilot-instructions.md` にリポジトリ全体の規則を追加しました。この規則が cloud agent の作成するプルリクエストに反映されることを、ハーネスの後半で確認します。

次は、Copilot cloud agent に[最初の Issue を割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズに使う指示ファイル][instruction-files]
- [カスタム指示のベスト プラクティス][instructions-best-practices]
- [Copilot のカスタム指示を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのリソース集][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
[awesome-copilot]: https://github.com/github/awesome-copilot
