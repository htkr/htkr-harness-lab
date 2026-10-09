# 公開リポジトリと会社のサーバーの間で非公開の作業メモを受け渡す方法（調査）

Issue #27 の調査結果。2026-10-10 時点で一次資料を読んだ。どの案を採るかは決めない。

## 前提

- このリポジトリは公開している。
- 作業メモ（会社のサーバー名、手順、組織に固有の設定）を自宅で書き、会社の Ubuntu サーバーで取り出したい。
- 利用者はサーバーの管理者で、Windows から SSH でサーバーに入る。

## 比較

| 案 | 手間 | 漏れる情報 | 鍵や権限を取り消せるか | サーバーに要るもの |
|---|---|---|---|---|
| A. 暗号化したファイルを公開リポジトリに置く（age、sops、git-crypt） | 小〜中。鍵を作り、サーバーに秘密鍵を置く | ファイル名、大きさ、変更した時期、コミットメッセージ。sops の YAML/JSON 形式ではキー名も平文。暗号文そのものは誰でも永久に取得できる | 取り消せない。鍵を替えても、古いコミットは古い鍵で読める。公開後は clone と fork から消せない | `age` または `sops`（apt や単体バイナリ）、秘密鍵ファイル |
| B. GitHub の非公開リポジトリ ＋ deploy key | 小。リポジトリを1つ作り、サーバーで作った公開鍵を登録 | GitHub（個人アカウント）に平文で置く。リポジトリ名は自分にしか見えない | 取り消せる。deploy key をリポジトリの設定から消せば、以後は読めない。ただしサーバーに clone 済みの内容は残る | `git`、SSH 鍵（パスフレーズ無しが普通） |
| C. GitHub の非公開リポジトリ ＋ 細かい権限のトークン（`gh` か HTTPS） | 小。トークンを作って `GH_TOKEN` などに入れる | B と同じ | 取り消せる。トークンを削除するか、期限切れを待つ | `git`（`gh` は任意）、トークン |
| D. secret gist | 最小 | URL を知る人には全部読める。GitHub は「非公開ではない」と明記 | URL の取り消しはできない（gist を消すことはできる） | `curl` か `git` だけ |
| E. パスワードマネージャーのメモ ＋ CLI（1Password `op`、Bitwarden `bw`） | 中。CLI を入れて、サーバーでサインインする | 事業者のクラウドに暗号化して置く | 取り消せる。サインアウト、セッションの失効、サービスアカウントのトークンの取り消し | `op` または `bw`、アカウントの認証情報（サーバーで入力） |

## A. 暗号化したファイルを公開リポジトリに置く

### git-crypt

git-crypt の README の Limitations 節に次の記述がある。

- ファイル名、コミットメッセージ、シンボリックリンクの先、その他のメタデータは暗号化しない。
- ファイルが変わったかどうか、ファイルの長さ、2つのファイルが同じかどうかを隠さない。
- 一度与えた読み取り権を取り消す機能が無い。鍵を途中で替えても、古い鍵を持つ人は古い履歴を読める。
- 「リポジトリの大部分が公開で、少数のファイルだけ暗号化したい」場面に向くと書いている。つまり公開リポジトリでの利用自体は想定している。

出典: https://github.com/AGWA/git-crypt （README.md の Limitations 節）

### sops

- YAML、JSON、ENV、INI 形式では、キーを平文のまま残し、値だけを暗号化する。BINARY 形式ではファイル全体を1つの塊として暗号化する。
  出典: https://getsops.io/docs/security/ （ソース: https://github.com/getsops/docs/blob/main/content/en/docs/security/_index.md ）
- 既定では YAML などの全部の値を暗号化してキーを平文で残す。`_unencrypted` などで一部を平文のままにもできる。
  出典: https://getsops.io/docs/usage/common-operations/
- age の秘密鍵は Linux では `$XDG_CONFIG_HOME/sops/age/keys.txt`、無ければ `$HOME/.config/sops/age/keys.txt` から読む。`SOPS_AGE_KEY_FILE` などで変えられる。
  出典: https://getsops.io/docs/usage/identities/age/
- 鍵が漏れたら `sops updatekeys` で鍵を外し、`sops rotate` でデータ鍵を作り直す、と書いている。これはファイルの今の版を守る手順で、Git の古いコミットにある暗号文は古い鍵で復号できるまま残る（この点は文書の記述からの推論）。
  出典: https://getsops.io/docs/usage/key-management/

### age

- `age -p` でパスフレーズによる暗号化ができる。SSH の公開鍵（`ssh-ed25519`、`ssh-rsa`）にも暗号化できる。
- SSH 鍵を使うと、暗号文に公開鍵のタグが入り、同じ鍵あてのファイルを追跡できる。SSH 鍵は認証用なら取り替えればよいので長く守られないことがある、とも注意している。
- `age-inspect` で、復号せずに受け手の種類と中身の大きさを表示できる。
- Ubuntu 22.04 以降は `apt install age`。

出典: https://github.com/FiloSottile/age （README の Passphrases、SSH keys、Inspecting encrypted files、Installation 節）

### 公開した後に消せないこと

GitHub の「リポジトリから機密データを削除する」文書に次の記述がある。

- 他の人の clone からは消せない。
- fork にコミットが残れば、そこから読める。
- キャッシュされた表示や pull request から SHA で辿れることがある。
- 最初にすべきことは秘密を無効にするか替えること。

出典: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository

### 推論（一次資料の記述ではない）

- 公開した暗号文は、誰でも取得してオフラインで試せる。パスフレーズが弱いか、将来に鍵が漏れれば、過去の全部の版が読める。取り消しの手段が無い。
- 暗号化していても、ファイル名、コミットの日時、大きさの変化から「会社の作業をこのリポジトリでしている」ことは分かる。

## B・C. GitHub の非公開リポジトリ

- 個人の無料プランで、非公開リポジトリを数の制限なく作れる（機能は一部制限あり）。
  出典: https://docs.github.com/en/get-started/learning-about-github/githubs-plans
- 非公開リポジトリは、本人と明示的に共有した人だけが読める。
  出典: https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories

### deploy key

- 1つのリポジトリだけに使える SSH 鍵。既定で読み取り専用。
- 欠点として、ふつうパスフレーズが無いのでサーバーが侵害されると使われること、期限が無いこと、人ではなくリポジトリに結び付くことを挙げている。

出典: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys

### 細かい権限のトークン（fine-grained PAT）

- 対象のリポジトリと権限（例: Contents の読み取りだけ）を絞れる。期限を付けることを強く勧めている。
- 不要になったら設定画面から削除する。トークンはパスワードと同じように扱う。

出典: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

### `gh auth login`

- 既定はブラウザで認証する。トークンはシステムの資格情報ストアに置き、無ければ平文のファイルに置く。
- 細かい権限のトークンは `GH_TOKEN` 環境変数で渡すことを勧めている。
- `gh auth login` でトークンを登録すると、そのアカウントの全リポジトリに届く権限になりうる（classic の最小スコープは `repo` など）。1つのリポジトリに絞るなら deploy key か細かい権限のトークンになる。

出典: https://cli.github.com/manual/gh_auth_login

## D. secret gist

- 「secret gist は非公開ではない。URL を知った人は、知らない人でも読める」と明記している。人目から守りたいなら非公開リポジトリを使うよう書いている。

出典: https://docs.github.com/en/get-started/writing-on-github/editing-and-sharing-content-with-gists/creating-gists

## E. パスワードマネージャーのメモと CLI

### 1Password CLI（`op`）

- `op read op://<vault>/<item>/<field>` で1つの欄を読む。`--out-file` でファイルに書ける。
  出典: https://www.1password.dev/cli/reference/commands/read/
- デスクトップアプリが無いサーバーでは `op account add` で、サインインのアドレス、メール、Secret Key、パスワードを入れる。セッションは操作が無いと30分で切れる。同じユーザーで動く別のプロセスがアカウントに触れうる、と注意している。
  出典: https://www.1password.dev/cli/sign-in-manually/
- サービスアカウントは人に結び付かない認証で、読める vault と操作を絞れる。トークンは替えるか取り消せる。
  出典: https://www.1password.dev/service-accounts/ 、 https://www.1password.dev/service-accounts/security/
- 料金は今回確認していない。

### Bitwarden CLI（`bw`）

- `bw login --apikey`（`BW_CLIENTID`、`BW_CLIENTSECRET`）でログインし、`bw unlock` でセッション鍵を作り `BW_SESSION` に入れる。API キーはマスターパスワードの代わりにはならない。
- `bw get notes <id>` で項目のメモを読む。
- Linux には npm、snap、単体バイナリで入れる。
- 終わったら `bw lock` か `bw logout` を実行するよう勧めている。

出典: https://bitwarden.com/help/cli/

- 無料の個人プランがあり、メモを保存できる。
  出典: https://bitwarden.com/pricing/

## 会社の情報を個人のクラウドに置くことについて（論点だけ）

結論は書かない。人間が確かめる点を挙げる。

- 社内規程が、会社の情報（サーバー名、手順、設定）を個人のクラウド（GitHub の個人アカウント、個人のパスワードマネージャー）に置くことを許すか。B〜E はどれもこれに当たる。A も、暗号化していても会社の情報を公開の場所に置くことになる。
- 社内規程が、会社のサーバーから個人のアカウントへ認証すること（deploy key、トークン、パスワードマネージャーへのサインイン）を許すか。
- メモのどの部分が会社の機密に当たるか。手順の一般的な部分と、サーバー名などの固有の値を分ければ、固有の値だけを会社の中に置く形も取れる。
- 会社が用意する置き場所（社内の Git サーバー、会社契約のパスワードマネージャーなど）があるか。
- 退職や異動のとき、個人のクラウドに残った会社の情報をどう消すか。
