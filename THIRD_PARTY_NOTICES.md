# 外部コンポーネントの告知と出自

このファイルには、このリポジトリにコピーまたは取り込んだ外部プロジェクトのコード、スキル、テンプレートなどを記録します。

パッケージに依存したりリンクを張ったりするだけで、そのライセンス全文を必ずここにコピーする必要があるわけではありません。実際の利用方法に応じて、元プロジェクトのライセンス要件に従ってください。

## 方針

外部の資料をコピーまたは取り込む前に、次を記録します。

- 元プロジェクトとリポジトリ
- 正確なバージョン、タグ、コミット
- 取り込むファイル・ディレクトリ
- 利用方法：依存関係、参照、取り込み、改変、再実装
- 元プロジェクトのライセンス
- 必要な帰属表示・告知
- ローカルでの変更内容（ある場合）

ライセンスや出自が不明な資料はコピーしないでください。

## 現在取り込んでいるコンポーネント

| コンポーネント | 配布元 | 版 | 取り込んだ場所 | 利用方法 | ライセンス |
|---|---|---|---|---|---|
| i-have-adhd | [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd) | 0.3.0（`839872f`） | `.agents/skills/i-have-adhd/` | 取り込み・改変 | MIT（Copyright (c) 2026 Ayoub Ghriss） |

i-have-adhd は、上流の `skills/i-have-adhd/SKILL.md` と `LICENSE` をコピーし、次を変えました。Claude Code からは `.claude/skills/i-have-adhd` のシンボリックリンクで読みます。

- 適用先を、Issue と PR の本文、それらへのコメントに絞った。チャットの返答とリポジトリ内の文書には適用しない。
- モデルが自分で呼べるように、`disable-model-invocation` を外した。
- 日本語の前置きと締めの決まり文句を、禁止の例に加えた。

## 参照しているコンポーネント

次のコンポーネントは、開発用ハーネスとして Claude Code のプラグインで参照しています。ソースはリポジトリにコピーしていません。版は `.claude/settings.json` の marketplace の `ref` で固定しています。

| コンポーネント | 配布元 | 固定したタグ | 利用方法 | ライセンス |
|---|---|---|---|---|
| pstack（`pstack@pstack-claude`） | [michael-denyer/pstack-claude](https://github.com/michael-denyer/pstack-claude) | `v0.9.80` | Claude Code プラグインとして参照 | MIT（Copyright (c) 2026 Lauren Tan, Michael Denyer） |
| Matt Pocock skills（`mattpocock-skills@mattpocock`） | [mattpocock/skills](https://github.com/mattpocock/skills) | `v1.3.1` | Claude Code プラグインとして参照 | MIT（Copyright (c) 2026 Matt Pocock） |

`docs/agents/` の3つの文書は、Matt Pocock skills の `setup-matt-pocock-skills` のひな型を日本語にし、このリポジトリに合わせて書き換えたものです。

## 評価・参照のみのコンポーネント

現在はPi、Oh My Pi、DeepSeek Harness、Matt Pocock skills、pstack、プレゼンテーション生成ライブラリなどを評価・参照しています。評価・参照しているだけでは、それらのソースコードをこのリポジトリに取り込んだことにはなりません。

## ライセンス本文

### i-have-adhd（`.agents/skills/i-have-adhd/`）

```text
MIT License

Copyright (c) 2026 Ayoub Ghriss

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### Matt Pocock skills（`docs/agents/` の元のひな型）

```text
MIT License

Copyright (c) 2026 Matt Pocock

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
