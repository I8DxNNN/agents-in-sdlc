---
title: "レッスン 9 - GitHub Copilot CLI のスラッシュコマンド"
description: "コンテキストの管理、モデルの選択、セッションの共有、任意のクラウド委任に使うスラッシュコマンドを確認します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

優れた CLI ツールと同様に、GitHub Copilot CLI には対話に使える多数のスラッシュコマンドがあります。これらのコマンドから、高度な機能、内部情報、追加の構成オプションを利用できます。これまでに `/diff`、`/mcp`、`/skills`、`/agent`、`/pr` などのコマンドを使いました。ここでは、ほかの便利なコマンドをいくつか確認します。

このレッスンでは、次のことを行います。

- `/context` と `/compact` を使って Copilot の会話コンテキスト管理を確認します。
- `/model` を使って利用可能なモデルを確認します。
- `/share` でセッションをエクスポートまたは共有する方法を学びます。
- 並行作業、worktree、cloud agent への委任に使う任意のコマンドを確認します。

## シナリオ

CLI の主要なワークフローが完了しました。次は、コンテキスト管理、モデルの切り替え、セッション共有、[Copilot cloud agent][about-cloud-agent]への任意の委任など、追加機能を確認しましょう。

## Copilot CLI のコンテキストを確認する

大規模または複雑なタスクに取り組むと、モデルの最大コンテキスト ウィンドウに達することがあります。Copilot CLI は必要に応じて会話を自動的に圧縮します。また、スラッシュコマンドを使って、自分で内容を確認したり圧縮したりできます。

1. Codespace に戻り、Copilot CLI がまだ開いていない場合は、リポジトリのルートから起動します。
2. 次のコマンドを入力します。

   ```plaintext
   /context
   ```

3. モデル、現在のトークン使用量、システム指示、ツール、メッセージ、空き領域にコンテキストがどのように分配されているかを確認します。
4. 会話を圧縮します。

   ```plaintext
   /compact
   ```

5. もう一度 `/context` を入力して結果を比較します。会話がまだ短い場合は、大きな違いが見られないことがあります。

> [!NOTE]
> コンテキスト ウィンドウが埋まると、Copilot CLI は自動的にコンテキストを圧縮します。自分でタイミングを選びたい場合は `/compact` を使います。無関係なタスクに切り替えて新しい会話を始める場合は、代わりに `/clear` または `/new` を使います。

## モデルを選ぶ

モデルごとに得意分野が異なり、開発者によって好みも異なります。Copilot CLI では、利用可能なモデルを一覧表示し、使うモデルを選択できます。

1. 次のコマンドを入力します。

   ```plaintext
   /model
   ```

2. 利用可能なモデルと使用状況の情報を確認します。
3. 現在のモデルを維持するか、別のモデルを選ぶか、<kbd>Esc</kbd> を押して一覧を閉じます。

## セッションを共有する

チームで協力し、得られた知見を共有すると、AI ツールの活用方法を改善できます。`/share` コマンドを使うと、セッションを Markdown または HTML ファイルにエクスポートしたり、共有可能なリンクを作成したり、GitHub gist として公開したりできます。

1. `/help` を入力し、インストール済みのバージョンで利用できる `/share` のオプションを確認します。
2. セッションを共有する場合は、ローカルの Markdown エクスポートに使う `/share file` など、目的に合った出力先を選びます。
3. エクスポートした内容を、誰かに送ったり公開したりする前に確認します。セッションのエクスポートには、プロンプト、応答、プロジェクトの詳細が含まれることがあります。

リンクや gist の公開は任意です。チームが共有を意図していないリポジトリや会話の内容を公開しないでください。

## 任意: 作業を並行化または委任する

ワークショップの主要な内容は完了しました。Copilot CLI には、大規模なタスクに使える次のコマンドもあります。

- `/fleet` は、独立したサブタスクをサブエージェントに分割し、並行して実行します。
- `/worktree` は、別のタスク用に独立した Git worktree を作成します。
- `/delegate` は、非同期で作業し、プルリクエストを作成することがある Copilot cloud agent にタスクを送信します。

これらのコマンドは追加の worktree やリモート作業を生み出すことがあるため、任意の演習です。試すときは、範囲を明確にした新しいタスクから始め、通常のワークフローで結果をレビューしてください。非同期エージェントの作業をさらに詳しく確認する場合は、[cloud agent ワークショップ][cloud-workshop]に進みます。

## まとめと次のステップ

Copilot CLI のスラッシュコマンドを使うと、設定やセッション共有を行い、内部の動作を確認できます。このレッスンでは、次のことを行いました。

- `/context` と `/compact` を使って Copilot の会話コンテキスト管理を確認しました。
- `/model` を使って利用可能なモデルを確認しました。
- `/share` でセッションをエクスポートまたは共有する方法を学びました。
- 並行作業、worktree、cloud agent への委任に使う任意のコマンドを確認しました。

Copilot CLI には、ほかにも多くのスラッシュコマンドがあります。学んだ内容を[振り返り][next-lesson]、さらに学ぶための次のステップを確認して、このワークショップを締めくくりましょう。

## リソース

- [Copilot CLI コマンド リファレンス][cli-reference]
- [Copilot CLI のコンテキスト管理][context-management]
- [Copilot cloud agent について][about-cloud-agent]

[previous-lesson]: ../8-create-pull-request/
[next-lesson]: ../10-review/
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[context-management]: https://docs.github.com/copilot/concepts/agents/copilot-cli/context-management
[about-cloud-agent]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-workshop]: ../../cloud/
