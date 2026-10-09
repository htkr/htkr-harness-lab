# AGENTS.md

## 作業の規約

- 実装の入口は pstack に統一する。Matt skills の `implement` と `implement-spec` では実装しない。
- 自律実行中はチャットで質問しない。人間の判断が要ることは判断チケットにし、今の Issue の範囲の外で見つけた作業は `needs-triage` の Issue にする。
- `main` に直接 push しない。作業はブランチと PR で行う。

## 自走時に取る Issue の順

人間がその場で応答しない実行（自律実行）では、次の順で Issue を1件ずつ取る。

1. `is:open label:wayfinder:research -is:blocked no:assignee` の判断チケットを、番号の若い順に取る。結果をその Issue にコメントして閉じる。どの案を採るかは決めない。
2. 1が無くなったら、`is:open label:ready-for-agent -is:blocked no:assignee` の実装 Issue を、番号の若い順に取る。
3. `wayfinder:grilling` の判断チケットは取らない。人間が対話のセッションで扱う。
4. 取ったら、最初の書き込みとして `gh issue edit <番号> --add-assignee @me` を実行する。

ラベルの意味と、`ready-for-agent` を付けてよい場面は `docs/agents/triage-labels.md` にある。

## Agent skills

### Issue tracker

Issue は GitHub Issues に置き、`gh` で操作する。`docs/agents/issue-tracker.md` を参照。

### Triage labels

triage のラベルは既定の名前（`needs-triage`、`ready-for-agent` など）をそのまま使う。判断チケットは `wayfinder:research` と `wayfinder:grilling` で区別する。`docs/agents/triage-labels.md` を参照。

### Domain docs

単一コンテキスト。用語集はルートの `GLOSSARY.md`、ADR は `docs/adr/` に置く。`docs/agents/domain.md` を参照。
