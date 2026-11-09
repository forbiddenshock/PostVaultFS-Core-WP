# PostVaultFS-Core-WP

　PostVaultFS-Core-WPはX（旧Twitter）への毎日の投稿を自動で取得し、**1日分の投稿を1記事（YYYY-MM-DD）にまとめて**自分のWordPressブログに自動で保存するツールです。

---

## ツールの特徴

- **メディア消失対策済み**：投稿内の画像や動画も自動でWordPressのメディアに格納します。
- **簡単セットアップ**：GitHub上の画面操作だけでセットアップが完了します。
- **自動実行**：Cloudflareの無料枠を使って自動的に実行でき、最短1分おきに自動実行できます。実行の間隔は1分〜59分で自由に変更可能です。
- **過去ログ取り込み可能**：Xから出力したアーカイブデータを、WordPressに一括インポートするツールも付属しています。
- **GitHubへのMarkdownファイル保存（オプション）**：AIなどに読み込ませる用として、「全投稿」「年度別投稿」「月別投稿」の形でMarkdownファイルとして記事を書き出す事ができます。
- **X APIによる安価な実装**：Xの投稿の保存ができるサービスは月額500円程度のものがありますが、PostVaultFS-Core-WPはXのAPIを使うためより安価に利用できます。作者の数字で言うと1ヶ月9万リクエストで月2ドル程度で、これは他サービスだと月額500円プランではまかなえない数字です。

---

## PostVaultFS-Core-WPを利用するにあたって必要な物

　PostVaultFS-Core-WPを利用するにあたって必要な物として次があります。

- **セルフホストしているWordPress**
- **GitHubのアカウント**
- **Cloudflareのアカウント**
- **Xのアカウント**

　WordPress以外は基本無料のサービスですのでアカウント登録をすればそのままアカウントが入手できます。
　WordPressは自分でセルフホストのWordPressを、**SSL対応の形式**で用意する必要があります。
　セルフホストのWordPressが用意できず、無料でなんとかしたい場合、このPostVaultFS-Core-WPの姉妹ツールであるPostVaultFS-Core-emDashを使うと無料でemDashを立ち上げてそこに投稿するようにできます。
　PostVaultFS-Core-emDashは開発完了、現在ドキュメント作成中なので公開までしばらくお待ちください。

---

## セットアップの事前準備

　PostVaultFS-Core-WPではセットアップに以下の情報を必要とします。各情報の取得方法はREADME後半に画像付きで載せますのでご参照下さい。
　これらの情報は設定が終わるまで全てメモ帳などで管理してください。

### 必要情報

- **WordPressの「サイトのURL」**
- **WordPressの「ログインユーザー名」**
- **WordPressの「アプリケーションパスワード」**
- **Cloudflareの「Account ID」**
- **Cloudflareの「API トークン」**
- **Xの「ユーザー名」**
- **Xの「Bearer Token」（または4つのキー）**
- **（オプション）GitHubの「トークン」**

---

### 1.WordPressから必要情報を取得する

　WordPressからは「サイトのURL」「ログインユーザー名」「アプリケーションパスワード」を取得します。

#### WordPressの「サイトのURL」

![wp-001](docs/images/wp-001.jpeg)

　自分のWordPressサイトを開いて、アドレスバーにあります。
　あとで「アプリケーションパスワード」を取る必要があるため、これは「http://」ではなく「https://」から始まる必要があります。

#### WordPressの「ログインユーザー名」

![wp-002](/Users/darktribe/Documents/img/readme/wp-002.jpeg)

　サイトにログインしている場合、サイト右上にアイコンがありますのでそれをクリックすると開くメニューに出ているのが「ログインユーザー名」です。

#### WordPressの「アプリケーションパスワード」

![wp-003](/Users/darktribe/Documents/img/readme/wp-003.jpeg)

　サイトにログインし、メニュー左側の「ユーザー」をクリックします。
　切り替わった右側の画面を下までスクロールし、「アプリケーションパスワード」の中にある「新しいアプリケーションパスワード名」に適当な名前を入力し（この名前は特に使わないので自由に決めていいです）したの「アプリケーションパスワードを追加」を押すと、ポップアップで新しいアプリケーションパスワードが表示されます。

　ここで表示される**アプリケーションパスワードは再表示できないので確実にメモっておいて下さい。**

---

### 2.Cloudflareから必要情報を取得する

　Cloudflareからは「Account ID」「API トークン」を取得します。

#### Cloudflareの「Account ID」

![cf001](/Users/darktribe/Documents/img/readme/cf001.jpeg)

　画面左側、「コンピュート」をクリックし、開いたメニューで「Workers & Pages」を開きます。

![cf002](/Users/darktribe/Documents/img/readme/cf002.jpeg)

　切り替わった画面、右側に「Account ID」があります。クリックしてコピーすると全てがコピーされるのでメモ帳などに貼り付けておいて下さい。

#### Cloudflareの「API トークン」

![cf003](/Users/darktribe/Documents/img/readme/cf003.jpeg)

　画面右上、人物アイコンをクリックして開くメニューで「プロフィール」をクリックします。

![cf004](/Users/darktribe/Documents/img/readme/cf004.jpeg)

　切り替わった画面の左側、「APIトークン」をクリックします。

![cf005](/Users/darktribe/Documents/img/readme/cf005.jpeg)

　切り替わった画面の右上、「トークンを作成する」をクリックします。

![cf006](/Users/darktribe/Documents/img/readme/cf006.jpeg)

　「トークン名」に適当な名前（ツールでは使わない情報です）を入れ、Policiesの中にある「Cloudflare Workers を編集する」をクリックします。

![cf007](/Users/darktribe/Documents/img/readme/cf007-4233478.jpeg)

　Policiesは3つ設定があります。順に「鉛筆マーク」をクリックして設定します。

![cf008](/Users/darktribe/Documents/img/readme/cf008.jpeg)

　ゾーン Policyは①を「含む」、②を「アカウントにある全てのゾーン」、③は自分のアカウントを選びます。特に設定ボタンなど押さず「Edit ゾーン Policy」の横の×ボタンで閉じて良いです。

![cf009](/Users/darktribe/Documents/img/readme/cf009.jpeg)

　アカウント Policyは①を「含む」、②を自分のアカウントを選び、下にある「開発者プラットフォーム」をクリックして開き、「D1」の項目を「編集」にチェックを入れます。特に設定ボタンなど押さず「Edit ゾーン Policy」の横の×ボタンで閉じて良いです。

　その後、画面右下の「概要に進む」をクリックし、切り替わった画面の右下の「トークンを作成する」を押すと、切り替わった画面でトークンが表示されます。

　**このトークンも再表示できないのでしっかりメモしておいて下さい。**

---

### 3.Xから必要情報を取得する

　Xからは「ユーザー名」「Bearer Token」を取得します。

#### Xの「ユーザー名」

　いつも使っているXのユーザー名です。「@」は不要です。
　例えば僕のユーザー名は「@showya_kiss」なので、X-Loggerで使うユーザー名は「showya_kiss」です。

#### Xの「Bearer Token」

　[X Developer Portal](https://developer.x.com/) でアプリを作成し、作成したアプリを開きます。

![x-01](/Users/darktribe/Documents/img/readme/x-01.jpeg)

　ベアラートークンという項目で再生成が可能なのでメモっておいて下さい

---

### （オプション）4.GitHubから必要情報を取得する

　記事をMarkdownでGitHubに保存したい人だけのオプションです。この機能が不要な人はこの作業をしなくて構いません。

![gh-001](/Users/darktribe/Documents/img/readme/gh-001.jpg)

　GitHubにログイン後、画面右上の自分のアイコンをクリックし、開いたメニューから「Settings」を選びます。

![gh-002](/Users/darktribe/Documents/img/readme/gh-002.jpeg)

　開いた画面を下がると、左側に「Developer settings」という項目があるのでそれをクリックします。

![gh-003](/Users/darktribe/Documents/img/readme/gh-003.jpeg)

　画面左側、「Personal access tokens」を開き、「Fine-grained tokens」をクリックします。
　開いた画面、右上に「Generate new token」があるのでクリックします。

![gh-004](/Users/darktribe/Documents/img/readme/gh-004.jpeg)

　順に設定していきます。
　①はトークン名なので好きに設定して下さい（X-Loggerでは使いません）。
　②はトークンが失効する期間です。厳密に管理すべきなのですが、僕は面倒なので「No Expiration」（失効期限なし）にしています。自己責任でお願いします。
　③はこのトークンがアクセスできるリポジトリを表します。少なくともForkしたリポジトリと、別のリポジトリにMarkdownを格納する場合そのリポジトリを選んで下さい。

![gh-005](/Users/darktribe/Documents/img/readme/gh-005.jpeg)

　④については、まず「Add Permissions」をクリックし、開いたメニューを下に移動、「Contents」にチェックを入れます。

![gh-006](/Users/darktribe/Documents/img/readme/gh-006.jpeg)

　自動で「Contents」と「Metadata」が入力されるので、「Contents」の「Access: Read-only」をクリックし、「Read and write」を選びます。
　最後に画面下左側の「Generate token」を選ぶと最終確認ダイヤログがでたあと「Generate token」ボタンを押せばトークンが発行されます。

---

　お疲れ様でした。
　これで必要情報が全て揃いました。

　続いてセットアップ方法を説明します。

---

## PostVaultFS-Core-WPセットアップ手順

　PostVaultFS-Core-WPのセットアップ手順は3ステップです。

1. このリポジトリを自分のGitHubにFork（コピー）する
2. GitHubに必要な情報をSecretsとして登録する
3. GitHub Actionsを実行してPostVaultFS-Core-WPのサービスを開始する

---

### ステップ1：このリポジトリを自分のGitHubにFork（コピー）する

　このリポジトリの右上にある「Fork」ボタンを押して、自分のGitHubアカウントにコピーします。

![fork-001](/Users/darktribe/Documents/img/readme/fork-001.jpeg)

![fork-002](/Users/darktribe/Documents/img/readme/fork-002.jpeg)

　もしMarkdown保存をする場合で、かつコピーしたリポジトリにMarkdownを格納したい場合、格納したMarkdownが見られてしまう可能性があるので、それを非公開にしたい場合はコピーしたリポジトリを「Settings」から「Private」に変更して下さい。

### ステップ2：GitHubに必要な情報をSecretsとして登録する

![sec-001](/Users/darktribe/Documents/img/readme/sec-001.jpeg)

　コピーしたリポジトリの画面上にある「Setting」タブをクリックします。

![sec-002](/Users/darktribe/Documents/img/readme/sec-002.jpeg)

　画面左側、「Secrets and variables」を開き、「Actions」をクリックします。

![sec-003](/Users/darktribe/Documents/img/readme/sec-003.jpeg)

　画面下、「New Repository secret」をクリックします。

![sec-004](/Users/darktribe/Documents/img/readme/sec-004.jpeg)

　この画面で必要情報を登録します。
　①「Name」の項目に「キー名」を入力します。
　②「Secret」の項目にSecretを入力します。
　③「Add secret」を押して確定します。

![sec-005](/Users/darktribe/Documents/img/readme/sec-005.jpeg)

　戻ってきた画面はこちらです。続いて「New repository secret」を押して登録を続けます。
　間違ったキー名を入れた場合はゴミ箱マークで消せますし、Secretを誤って入れた場合は鉛筆マークで上書きできます。
　入力が必要な物は以下です。

#### 【必須で登録する項目】
| Name（キー名） | Secret（入力する内容） |
| :--- | :--- |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare の Account ID |
| `CLOUDFLARE_API_TOKEN` | Cloudflare の API トークン |
| `X_USERNAME` | 自分の X ユーザー名（`@` は付けない。例：`showya_kiss`） |
| `X_BEARER_TOKEN` | X API の Bearer Token（※下の4つのキーを使う場合は不要） |
| `WP_URL` | WordPress の URL（例：`https://xlog.example.com` ※最後の `/` は不要） |
| `WP_USERNAME` | WordPress のユーザー名 |
| `WP_APP_PASSWORD` | WordPress のアプリケーションパスワード |

#### 【オプション：GitHub にも Markdown で保存したい場合のみ追加】
| Name（名前） | Secret（入力する内容） |
| :--- | :--- |
| `GH_SYNC_TOKEN` | 事前準備④で作った GitHub のトークン |
| `GH_SYNC_REPO` | 保存先のリポジトリ名（例：`forbiddenshock/PostVaultFS-Core-WP`。空欄ならこのリポジトリ自体に保存されます） |

### ステップ3：GitHub Actionsを実行してPostVaultFS-Core-WPのサービスを開始する

![run-001](/Users/darktribe/Documents/img/readme/run-001.jpeg)

　リポジトリ上部にある「Actions」をクリックします。

![run-002](/Users/darktribe/Documents/img/readme/run-002.jpeg)

　もし「Workflows aren't being run on this forked repository」と表示されたら、緑色の 「I understand my workflows, go ahead and enable them」を押して有効化します。

![run-003](/Users/darktribe/Documents/img/readme/run-003.jpeg)

左側のリストから「Deploy PostVaultFS-Core-WP to Cloudflare Workers」をクリックします。

![run-004](/Users/darktribe/Documents/img/readme/run-004.jpeg)

①画面右上の「Run workflow」ボタンを押すとメニューが開きます。

②Cronの実行間隔を分数で指定します。「どれくらいの頻度でXの投稿をチェックするのか」の間隔です。
③GitHubに出力するMarkdownの種別です。year,month,fullの中から必要な物だけ残します。不要な場合は全部削除して下さい。
・year = 年ごと。Year2026.mdという形式で出力される。
・month = 月ごと。Month202611.mdという形式で出力される。
・full = 全てのデータ。Fulldata.mdとして出力される。
④ただのリポスト(RT)も取得して記録するか。Yesなら「true」Noなら「false」にします。

　最後に下にある「Run workflow」を押せば実行開始です。1分ほど待って、緑のチェックマークがつけば完了です。

　あとは指定した間隔でXの投稿がチェックされ、WordPressの「今日の記事」に追記、上書きされていきます。

※GitHubに保存されるMarkdownはAIなどに分析させると面白いデータが取れるかもしれません。



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

---

Document by 如月翔也(@showya_kiss) from [ForbiddenShockWorks](https://forbiddenshock.com)
