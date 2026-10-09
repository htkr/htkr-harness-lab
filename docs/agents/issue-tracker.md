# Issue の置き場所

このリポジトリの Issue と仕様は GitHub Issues に置く。操作にはすべて `gh` CLI を使う。

## 操作

- 作成する：`gh issue create --title "..." --body "..."`。本文が複数行になるときはヒアドキュメントを使う。
- 読む：`gh issue view <number> --comments`。コメントは `jq` で絞り、ラベルも取得する。
- 一覧を出す：次のコマンドを使う。必要に応じて `--label` と `--state` で絞る。

  ```sh
  gh issue list --state open --json number,title,body,labels,comments \
    --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'
  ```

- コメントする：`gh issue comment <number> --body "..."`
- ラベルを付ける・外す：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- 閉じる：`gh issue close <number> --comment "..."`

リポジトリは `git remote -v` から決まる。clone の中で実行すれば `gh` が自動で判定する。

## 実装 Issue のテンプレート

`ready-for-agent` を付ける実装 Issue は、本文を次の形にする。

```markdown
## What to build

（何を作るか。利用者から見た振る舞いで書く）

## 仕様

仕様 Issue または未定（#15）

## 受け入れ条件

- [ ] （実物で確かめられる条件）
```

「仕様」欄の書き方は #15 で決める。決まるまでは、仕様 Issue があればその番号を書き、無ければ「未定（#15）」と書く。取る順は `AGENTS.md` の「自走時に取る Issue の順」にある。

## PR を triage の対象にするか

**PRs as a request surface: no.**（外部からの PR を要望として扱うなら `yes` にする。`/triage` がこの値を読む）

`yes` のときは、PR にも Issue と同じラベルと状態を使う。操作は `gh pr` で行う。

- PR を読む：`gh pr view <number> --comments`。差分は `gh pr diff <number>`。
- triage する外部 PR の一覧を出す：次のコマンドを実行する。結果から `authorAssociation` が `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR`、`NONE` のものだけを残す。`OWNER`、`MEMBER`、`COLLABORATOR` は除く。

  ```sh
  gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments
  ```

- コメント・ラベル・クローズ：`gh pr comment`、`gh pr edit --add-label` / `--remove-label`、`gh pr close`。

GitHub では Issue と PR が同じ番号の列を使う。`#42` だけでは区別できないので、`gh pr view 42` を試し、失敗したら `gh issue view 42` を使う。

## スキルが「Issue トラッカーに公開する」と言ったとき

GitHub の Issue を作る。

## スキルが「関連するチケットを取得する」と言ったとき

`gh issue view <number> --comments` を実行する。

## Wayfinder の操作

`/wayfinder` が使う。Map は1つの Issue で、その子 Issue が判断チケットになる。Map の無い判断チケットも、同じラベルと依存で扱う。

- Map：`wayfinder:map` ラベルを付けた1つの Issue。本文に Notes、Decisions-so-far、Fog を書く。作成は `gh issue create --label wayfinder:map`。
- 子チケット：Map の GitHub sub-issue としてつなぐ。登録は sub-issues のエンドポイントに `gh api` で行う。sub-issues が使えない場合は、Map の本文のタスクリストに子を加え、子の本文の先頭に `Part of #<map>` を書く。ラベルは `wayfinder:<type>`（`research` / `prototype` / `grilling` / `task`）。着手したら、作業する開発者を assignee にする。
- ブロック関係：GitHub の issue dependencies で表す。UI に表示されるので、これを正とする。追加は次のコマンドで行う。

  ```sh
  gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>
  ```

  `<blocker-db-id>` はブロックする側の数値の database id である。`gh api repos/<owner>/<repo>/issues/<n> --jq .id` で取得する。`#number` や `node_id` ではない。開いているブロッカーの数は `issue_dependencies_summary.blocked_by` に出る。dependencies が使えない場合は、子の本文の先頭に `Blocked by: #<n>, #<n>` を書く。ブロッカーがすべて閉じたら、そのチケットに着手できる。
- 次に着手するチケットの選び方：
  1. Map の子のうち開いているものを一覧する。`gh issue list --state open` の結果を、Map の sub-issues かタスクリストに絞る。
  2. 開いたブロッカーがあるものを除く。`issue_dependencies_summary.blocked_by > 0` のものと、`Blocked by` 行に開いた Issue を含むものである。
  3. assignee がいるものを除く。
  4. 残りのうち Map での順番が最初のものを選ぶ。
- 着手：セッションで最初の書き込みとして `gh issue edit <n> --add-assignee @me` を実行する。
- 解決：`gh issue comment <n> --body "<answer>"`、`gh issue close <n>` の順に実行する。Map があれば、そのあと Map の Decisions-so-far に要点とリンクを追記する。
