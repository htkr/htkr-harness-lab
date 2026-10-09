# triage のラベル

Matt skills は triage の役割を決まった名前で呼ぶ。この文書は、その役割名とこのリポジトリの GitHub のラベル名を対応づける。あわせて、判断チケットのラベルと、`ready-for-agent` を付けてよい場面を定める。

## 状態

| mattpocock/skills の役割名 | このリポジトリのラベル | 意味 |
|---|---|---|
| `needs-triage` | `needs-triage` | メンテナーが評価する |
| `needs-info` | `needs-info` | 起票者の追加情報を待つ |
| `ready-for-agent` | `ready-for-agent` | 内容が実装できるまで固まり、無人のエージェントが着手できる |
| `ready-for-human` | `ready-for-human` | 人間が実装する |
| `wontfix` | `wontfix` | 対応しない |

## 分類

| mattpocock/skills の役割名 | このリポジトリのラベル | 意味 |
|---|---|---|
| `bug` | `bug` | 壊れている |
| `enhancement` | `enhancement` | 新機能か改善 |

スキルが役割名で指示したら（例「AFK で着手できるラベルを付ける」）、表の右の列のラベルを使う。別のラベル名に変えるときは、右の列を書き換える。

## 判断チケット

問いを解決する Issue を判断チケットと呼ぶ。1つの変更を実装する実装 Issue とは分ける。判断チケットには状態と分類のラベルを付けず、次のどちらかを付ける。

| ラベル | 進め方 | 自律実行中のエージェント |
|---|---|---|
| `wayfinder:research` | 事実を調べて答える。結果を Issue にコメントして閉じる。どの案を採るかは決めない | 取る |
| `wayfinder:grilling` | 人間との対話で決める。決まったら決定をコメントして閉じる | 取らない |

判断チケットの結論を前提にする Issue は、GitHub の依存（blocked by）でつなぐ。

## `ready-for-agent` を付けてよい場面

`ready-for-agent` は「内容が実装できるまで固まり、ランナーが取ってよい状態」を表す。誰が付けたかは問わない（[tickdeck#59](https://github.com/htkr/tickdeck/issues/59)）。

エージェントが付けてよいのは、人間が起動した対話セッションの中だけである。`/triage`、判断チケットの解決で作る Issue、`/to-tickets` が当たる。

自律実行中のエージェントは付けない。今の Issue の範囲の外で見つけた作業は、Issue にして `needs-triage` を付ける。人間の判断が要るものは判断チケットにする。

次の Issue には付けない。

- 決まっていない判断が残る Issue。先に判断チケットに切り出す。
- 振る舞いを変えるのに、仕様がまだ決まっていない Issue。仕様の置き場所は #15 で決める。
- 手触りで良し悪しが決まる Issue。先に人間が試作で案を選ぶ。

依存が残っていても付けてよい。取れるかどうかは `-is:blocked` で判定する。
