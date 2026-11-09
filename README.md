# x-logger かんたん導入ガイド

X（旧Twitter）の毎日の投稿を自動で取得し、**1日1記事（`YYYY-MM-DD`）** にまとめて自分の WordPress ブログへ自動保存するツールです。

- **リンク死対策済み**：投稿内の画像や動画も自動で WordPress 内のメディアにコピーして表示します。
- **サーバー契約・コマンド操作不要**：GitHub 上の画面操作だけでセットアップが完了し、Cloudflare の無料枠（Cron）で最短1分おきに自動実行されます（実行間隔は1分〜59分まで自由に変更可能）。
- **過去ログ取り込み対応**：Xのアーカイブデータ（ZIP）から過去ログを一括インポートするツールも付属しています。
- **GitHubへのMarkdown保存（オプション）**：`Year2026.md` / `Month202611.md` / `Fulldata.md` の形式で GitHub 上にもテキストログを残せます。

---

## 事前準備：必要なキーを用意する

作業を始める前に、メモ帳などに以下の情報を控えておいてください。

### ① WordPress の情報（必須）
1. 自分の WordPress 管理画面にログインし、**「ユーザー」＞「プロフィール」** を開きます。
2. 一番下にある **「アプリケーションパスワード」** の欄で、適当な名前（`x-logger` など）を入力して **「新しいアプリケーションパスワードを追加」** を押します。
3. 表示されたパスワード（スペース入りの英数字）をメモします。
   - 控えるもの：**サイトのURL**、**WordPressのログインユーザー名**、**アプリケーションパスワード**

### ② Cloudflare の情報（必須・無料）
1. [Cloudflare ダッシュボード](https://dash.cloudflare.com/) にログインします。
2. 左メニューの **「Workers & Pages」** を開き、画面右側に表示されている **Account ID** をコピーしてメモします（※ドメイン個別の「ゾーンID」ではなく「Account ID」です）。
3. 右上の自分のアイコン ＞ **「プロファイル」＞「APIトークン」＞「トークンを作成する」** を開きます。
4. **「Cloudflare Workers を編集する (Edit Cloudflare Workers)」** の横にある **「テンプレートを使用」** を押します。
5. 以下の2箇所を設定してトークンを発行し、メモします：
   - **アカウントリソース (Account Resources)**：「包含 (Include)」で **自分のアカウント（または「すべてのアカウント」）** を選択
   - **ゾーンリソース (Zone Resources)**：「包含 (Include)」で **「すべてのゾーン (All zones)」** を選択
   - ※「クライアント IP アドレス フィルタリング」は**空欄のまま**にしてください（GitHub Actions から接続できなくなります）。
   - 控えるもの：**Account ID**、**APIトークン**

### ③ X (旧Twitter) API の情報（必須）
1. [X Developer Portal](https://developer.x.com/) で App を作成し、**Bearer Token**（または **API Key / API Key Secret / Access Token / Access Token Secret** の4つ）を取得してメモします。
   - 控えるもの：**自分のXユーザー名（`@`を除いたID）**、**Bearer Token（または4つのキー）**

### ④ GitHub のトークン（Markdownでも保存したい方のみ・任意）
1. GitHub 右上のアイコン ＞ **Settings ＞ Developer settings ＞ Personal access tokens ＞ Fine-grained tokens** からトークンを発行します。
2. 対象リポジトリを選び、**Repository permissions ＞ Contents** を **Read and write** に設定して発行し、メモします。

---

## セットアップ手順（全3ステップ）

パソコンにソフトをインストールする必要はありません。すべてブラウザ上の GitHub 画面だけで完結します。

### ステップ1：このリポジトリを自分の GitHub にコピー（Fork）する
1. このリポジトリの右上にある **「Fork」** ボタンを押して、自分の GitHub アカウントにコピーします。
   （※もし Markdown 保存もこのリポジトリに行う場合は、非公開にしたいならリポジトリ作成後に Settings から Private に変更してください）

### ステップ2：GitHub の「Secrets」にキーを登録する
1. コピーした自分のリポジトリ画面で、上部の **「Settings」** タブを開きます。
2. 左メニューの **「Secrets and variables」＞「Actions」** をクリックします。
3. **「New repository secret」** という緑色のボタンを押し、事前準備でメモした内容を1つずつ登録します。

#### 【必須で登録する項目】
| Name（名前） | Secret（入力する内容） |
| :--- | :--- |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare の Account ID |
| `CLOUDFLARE_API_TOKEN` | Cloudflare の API トークン |
| `X_USERNAME` | 自分の X ユーザー名（`@` は付けない。例：`showya_kiss`） |
| `X_BEARER_TOKEN` | X API の Bearer Token（※下の4つのキーを使う場合は不要） |
| `WP_URL` | WordPress の URL（例：`https://xlog.example.com` ※最後の `/` は不要） |
| `WP_USERNAME` | WordPress のユーザー名 |
| `WP_APP_PASSWORD` | WordPress のアプリケーションパスワード |

*(※ X API で Bearer Token の代わりに OAuth 1.0a キーを使う場合は、`X_API_KEY`、`X_API_SECRET`、`X_ACCESS_TOKEN`、`X_ACCESS_SECRET` の4つを登録してください)*

#### 【オプション：GitHub にも Markdown で保存したい場合のみ追加】
| Name（名前） | Secret（入力する内容） |
| :--- | :--- |
| `GH_SYNC_TOKEN` | 事前準備④で作った GitHub のトークン（※`GITHUB_` で始まる名前はGitHubの仕様で登録できないため `GH_` にしています） |
| `GH_SYNC_REPO` | 保存先のリポジトリ名（例：`自分のID/x-logger`。空欄ならこのリポジトリ自体に保存されます） |

### ステップ3：GitHub Actions を実行して自動稼働をスタートする
1. リポジトリ上部の **「Actions」** タブを開きます。
2. 「Workflows aren't being run on this forked repository」と表示されたら、緑色の **「I understand my workflows, go ahead and enable them」** を押して有効化します。
3. 左側のリストから **「Deploy x-logger to Cloudflare Workers」** をクリックします。
4. 右側にある **「Run workflow」** ボタンを押します。
   - **Cronの実行間隔（分単位）**：デフォルトは最短の `1`（毎分実行）です。もし15分おきや30分おきにしたい場合は `15` や `30` に変更できます。
   - **GitHubに出力するMarkdown種別**：`year,month,full` の中から欲しいものを残します（例：年別と月別だけなら `year,month`）。
     - `year` ＝ `Year2026.md`（年ごと）
     - `month` ＝ `Month202611.md`（月ごと）
     - `full` ＝ `Fulldata.md`（全期間まとめ）
   - **単純なリポスト(RT)も取得して記録するか**：記録するなら `true`、除外するなら `false` にします。
5. 緑色の **「Run workflow」** を押して、1分ほど待って緑色のチェックマーク（✔）が付けば完了です！

**これだけで、あとは指定した間隔（デフォルト1分おき）で自動でXの投稿がチェックされ、WordPressの「今日の記事」にどんどん追記・上書き保存されていきます。**

---

## おまけ：過去のXアーカイブ（過去ログ）を取り込む方法

X公式からダウンロードした過去のアーカイブデータ（ZIPを解凍したフォルダ）を一括で取り込みたい場合は、付属の `importer/index.html` を使います。

1. `importer/index.html` をパソコンにダウンロードして、そのままダブルクリックでブラウザ（Chrome / Edge / Safari など）で開きます。
2. **「1. Xアーカイブデータの読み込み」** で、解凍したアーカイブフォルダ（`data` フォルダ）を選択します。
3. **「3. WordPress 設定」** に自分の WordPress の URL・ユーザー名・アプリケーションパスワードを入力します。
4. 一番下のボタンからやりたい処理を選んで押すだけです：
   - **「WordPress インポート用 XML (WXR) を生成してダウンロード」**：
     アーカイブ内の画像・動画を WordPress のメディアへアップロードしてリンク切れを防いだ上で、WordPress 管理画面の「ツール ＞ インポート ＞ WordPress」から読み込める XML ファイルを作成します。
   - **「WordPress REST API へ直接メディア＋日付別記事を送信」**：
     XML を介さず、そのままブラウザから直接 WordPress に過去の日付別記事と画像・動画を一括投稿します。
   - **「選択した GitHub 用 Markdown (.md) を生成してダウンロード」**：
     `Year2026.md` / `Month202611.md` / `Fulldata.md` のうちチェックを入れた形式で、画像・動画を除いた過去ログ Markdown を一括ダウンロードします。
