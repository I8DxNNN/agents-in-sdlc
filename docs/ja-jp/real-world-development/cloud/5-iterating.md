---
title: "演習 5: GitHub Copilot の作業を反復的に改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このラボでは、ユーザー体験の改善と機能追加を目的に、GitHub Copilot にいくつかのタスクを依頼しました。コードにドキュメントを追加し、デザイン チームが改善できる関連ゲーム機能を作成し、ハイ コントラストとライト モードの切り替えを含むアクセシビリティ機能を実装しました。コード変更を確認し、必要に応じて Copilot にフィードバックを返して改善しましょう。

### シナリオ

これまでに何度も確認したとおり、生成 AI を導入してもソフトウェア設計と DevOps の基本は変わりません。生成されたコードをレビューし、通常の DevOps プロセスに沿って作業します。チーム全体でレビューを有効にする前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能について GitHub Copilot が提案した内容を確認しましょう。

## セキュリティと GitHub Copilot cloud agent

Copilot cloud agent は非同期かつ監督なしでタスクを実行するため、安全性を保つためのセキュリティ制約が設けられています。具体的には、次のとおりです。

- Copilot はリポジトリへの読み取りアクセスのみを持ち、書き込みアクセスはコード用に使用するブランチ **のみ** に許可されます。
- Cloud agent は GitHub Actions 内で実行され、個別の一時的な環境を作成して作業します。
- GitHub Actions ワークフローを実行するには、人間による承認が必要です。
- MCP サーバーを含む[外部リソースへのアクセスは、既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。

## 生成されたドキュメントをレビューする

まず、コードにドキュメントを追加するために GitHub Copilot cloud agent が作成した最初のプルリクエスト（PR）を確認します。github.com の標準的な PR インターフェイスを使います。

> [!NOTE]
> PR を確認するときに、ファイアウォールによって GitHub Copilot がブロックされたという警告が表示されることがあります。Copilot は MCP サーバーへの外部呼び出しを含む外部リソースへのアクセスが既定で制限されているため、これは **想定どおりの動作** です。必要であれば、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、プルリクエストの一覧を開きます。
3. **Add missing documentation** または同様のタイトルのプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストには **[WIP]** フラグが付きます。その場合は、Copilot が作業を完了するまで待ちます。数分かかることがあるため、休憩したり、これまでに学んだことを振り返ったりしてください。

4. プルリクエストの準備ができたら、**Files changed** タブを選択し、変更をレビューします。

    ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. TSDoc のドキュメントコメントなど、更新されたコードとドキュメントを確認します。変更内容は状況によって異なります。

    変更を確認するときは、TSDoc のドキュメントコメント、TypeScript の規約、コメント ヘッダーを探します。これらは演習 2 の最初に確認したカスタム指示ファイルによるものです。

6. 更新内容をレビューして問題がなければ、**Conversation** タブに戻って下にスクロールします。
7. 一部のワークフローが承認待ちになっていることを示す表示を確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかることがあります。

## GitHub Copilot に変更を依頼する

プルリクエストで Copilot と作業するのは一方通行ではありません。チーム メンバーに依頼するのと同じように、プルリクエストのコメントやコードのインライン コメントで Copilot にメンションできます。Copilot はコメントを確認し、変更に対応する新しいセッションを開始します。結果は確定的ではないため、必ずこう依頼すべきという文面はありません。次のような変更を依頼できます。

- 各コード ファイルの先頭に、ファイルの内容を簡潔に説明するコメント ヘッダーを追加する。
- TypeScript と Astro のファイルに TSDoc のドキュメントコメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 上記の例など、コードベースに必要なドキュメントの変更をコメントで依頼し、ほかのユーザーと同じように **@copilot** をメンションします。
2. **View Session** を選択し、Copilot の作業を確認します。Copilot が更新用の新しいセッションを開始することに注目してください。
3. **Back to pull request** を選択すると、プルリクエストに戻れます。

    ![Back to pull request](../../../_images/ex7-back-to-pr.png)

4. Copilot の変更が完了すると、プルリクエストに新しいコミットが表示されます。
5. **Files changed** タブを選択し、変更をレビューします。

納得できるまで反復を続けます。問題がなければ、PR をドラフトからレビュー可能な状態に変更し、`main` ブランチにマージします。

![PR をレビュー可能な状態にする](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

ゲーム詳細ページに関連ゲームを表示する Issue を解決するため、Copilot が作成した PR に戻ります。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** または同様のタイトルの PR を選択します。
4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 更新内容をレビューして問題がなければ、**Conversation** タブに戻って下にスクロールします。
6. 一部のワークフローが承認待ちになっていることを示す表示を確認します。
7. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

8. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかることがあります。
9. **任意:** Codespace でこのブランチに切り替えて、関連ゲーム機能を手動でテストできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します（`<branch-name>` は Copilot が作成したブランチ名に置き換えます。例: **copilot/fix-8**）。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

Copilot が関連ゲーム機能を実装しました。これまでと同様に、Copilot cloud agent に更新を依頼して反復できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーと TSDoc のドキュメントコメントを追加したりできます（この Issue はカスタム指示を更新する **前** に割り当てたことに注意してください）。前と同じように、**Conversation** タブにコメントを追加すると、Copilot が内容を確認して新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ カスタム エージェントを使って実装した機能をレビューします。この PR には、演習 3 で依頼したハイ コントラスト モードと、演習 4 の mission control で追加を依頼したライト モードの両方が含まれているはずです。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** または同様のタイトルの PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、プルリクエストには **[WIP]** フラグが付きます。その場合は、Copilot が作業を完了するまで数分待ちます。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 実装をレビューするときは、特に次の点を確認します。
   - モード切り替え用の UI コンポーネント。
   - ユーザー設定を保持するためのローカル ストレージの使用。
   - ハイ コントラスト モードとライト モード用の CSS またはスタイルの変更。
   - アクセシビリティ属性（ARIA ラベル、キーボード操作など）。
   - モード切り替えを管理する JavaScript/TypeScript コード。

6. 更新内容をレビューして問題がなければ、**Conversation** タブに戻って下にスクロールします。
7. 一部のワークフローが承認待ちになっていることを示す表示を確認します。
8. ワークフローが承認待ちの場合は、**Approve and run workflows** を選択します。

   ![Approve and run workflows](../../../_images/shared-approve-workflows.png)

9. プルリクエストのチェック欄にワークフローが登録されます。問題がなければ、単一 Astro アプリのプロジェクト チェックが成功します。完了まで数分かかることがあります。
10. **任意:** Codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルを開いて、次のコマンドを実行します（`<branch-name>` は Copilot が作成したブランチ名に置き換えます）。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    その後、アプリケーションを起動し、ブラウザーでハイ コントラストとライト モードの切り替えをテストします。ページを再読み込みしても設定が保持されることを確認してください。

アクセシビリティ カスタム エージェントが、アクセシビリティのベスト プラクティスに沿った実装を Copilot に促したことを確認します。問題や改善点があれば、前の PR と同様にコメントで **@copilot** をメンションして更新を依頼できます。

## 任意の演習 — 委任を続ける

Cloud agent は、実際のバックログ項目を任せて別の作業に取り組むと効果を発揮します。習慣を身につけるために、リポジトリにいくつか Issue を登録し、Copilot に割り当ててみましょう。次のようなタスクが考えられます。

- ゲーム詳細ページに支援者向けの関心フォームを作成する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent ハーネスを完了しました。これらのレッスンを通じて、次のことを行いました。

- このリポジトリに含まれるカスタム指示ファイルを確認し、cloud agent の出力に与える影響を確認しました。
- Issue を Copilot cloud agent に割り当て、環境のセットアップ、計画、非同期の実行を確認しました。
- アクセシビリティ用のカスタム エージェントを作成して使用し、ハイ コントラストとライト モードの切り替えを追加しました。
- Copilot agents ページを mission control として使い、アクセシビリティ セッションを監視して指示を追加しました。
- Copilot のプルリクエストをレビューして反復し、**@copilot** に更新を依頼し、ワークフローを承認しました。

## レビューと次のステップ

Cloud agent ハーネスを完了しました。さらに学びたい場合は、ほかのハーネスで今回の内容を補完できます。

- 🖥️ **[VS Code ハーネス](../../vscode/)** — IDE から Copilot Chat のエージェント モードと MCP 統合を試します。
- 💻 **[CLI ハーネス](../../cli/)** — Plan と Autopilot モード、エージェント スキル、カスタム QA エージェント、Playwright MCP、Agent Merge、cloud agent に戻る任意の `/delegate` ブリッジを使い、ターミナルからレビュー済みの変更を 2 つリリースします。

自分のリポジトリでは、次の作業も試してみてください。

- リファクタリングやテスト カバレッジの Issue を cloud agent に割り当てる。
- 別の分野に特化したカスタム エージェントを作成する。
- 別の技術スタック向けに `copilot-setup-steps.yml` を設定する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent セットアップ ワークフローのベスト プラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall

[previous-lesson]: ../4-managing-agents/
