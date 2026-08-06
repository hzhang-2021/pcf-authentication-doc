**Sign Up with Google Form**

Googleのアカウントを使ってPubcasefinderサーバへSignUp仕組み

1.  Userが「Sign Up」をスタート

2.  Flask側、Configファイルに定義されたGoogle Form URLへRedirect

3.  Google Formをユーザー側へ帰す

4.  ユーザーが名前、メールアドレス、Affiliation,JobTitle,Groupなど入力して、Submitする

5.  Google Form側情報を保存して、GoogleSpreadsheetの「Submit
    Trigger」を自動的に呼び出す。

6.  GoogleSpreadsheetのSubmit
    Triggerを実行、Pubcasefinderサーバへユーザー新規追加を投げて、AuthenticationCodeを取って、ユーザーのメールアドレスへAuthenticationメールを送信

7.  Pubcasefinderサーバのインタフェースです。新ユーザーをMySQLのAccout_authテーブルへ登録、AuthenticationCodeを作成して、GoogleSpreadsheetへ帰す

8.  ユーザー側、Authenticationメール中のリンクをクリックして、Authenticationを行います。

9.  Pubcasefinderサーバで、ユーザーのAuthenticationを受けて、Account_authテーブルへ登録。

10. GoogleSpreadsheetのAuthentication Check
    Triggerを実行、定期的に、PubcasefinderサーバへAuthentication完了するかを確認する。完了したものあれば、管理者のメールアドレスへNotificationメールを送信する。

11. Pubcasefinderサーバのインタフェースです。ユーザーのAuthentication状態を返す。

12. 管理者がNotificationメールを受信、GoogleSpreadsheetのサイトへアクセスして、新規ユーザーの登録はYES
    or Noは、画面のMenuより、行いました。

13. GoogleSpreadsheetのMenu機能です。管理者の決定は、Pubcasefinderサーバとユーザーへ動作する。

下に、例として、「https://staging-pubcasefinder.dbcls.jp」サーバのpanelsearch(nanbyo)用GoogleFormの作成を説明する。

1.  ご利用のGoogle AccountでログインGoogleへアクセス

![](media/image1.png){width="7.268055555555556in"
height="6.773611111111111in"}

「Forms」というGoogle APPをクリックする

![](media/image2.png){width="7.268055555555556in"
height="6.773611111111111in"}

「Start a new form」をクリックする

![](media/image3.png){width="7.268055555555556in"
height="6.773611111111111in"}

「Settings」タブを選択し、「Responses」部分を設定する。

⁂「Make this a quiz」部分は押さないで

![](media/image4.png){width="7.268055555555556in"
height="6.513888888888889in"}

「Presentation」部分を設定する。特に、Confirmation
message部分は編集する。

![](media/image5.png){width="7.268055555555556in" height="4.1in"}

「Questions」タブを選択、FormのTitleを編集、Formの各Questionを以下の順番で追加する。

  ------------------------------------------------------------------------------
  **Title**                      **Required**            **Validation**
  ------------------------------ ----------------------- -----------------------
  **苗字**                       ON                      NO

  **Last name (English)**        ON                      NO

  **名前**                       ON                      NO

  **First name (English)**       ON                      NO

  **所属機関・部署**             ON                      NO

  **所属機関のメールアドレス**   ON                      YES

  **職名**                       ON                      NO

                                                         

                                                         

                                                         

                                                         
  ------------------------------------------------------------------------------

![](media/image6.png){width="7.268055555555556in"
height="4.069444444444445in"}

特に、「**所属機関のメールアドレス**」部分は、下右の三点マックよりResponse
validationを設定する。
把这个转换为github markdown
把这个转换为github markdown
---

## 使用方法
# Google Form 登録システム - Pubcasefinder

## 概要

本ドキュメントでは、Google Form を利用した Pubcasefinder サーバーのサインアップシステムの実装について説明します。

---

## システムアーキテクチャ

### ユーザー登録フロー

```mermaid
graph TD
    A[ユーザーが Sign Up をクリック] --> B[Flask が Google Form URL へリダイレクト]
    B --> C[Google Form を表示]
    C --> D[ユーザーがデータを入力して送信]
    D --> E[データを Google Spreadsheet に保存]
    E --> F[Submit Trigger を実行]
    F --> G[MySQL Account_auth テーブルに新規ユーザーを登録]
    G --> H[認証コードを生成]
    H --> I[ユーザーに認証メールを送信]
    I --> J[ユーザーがメールのリンクをクリック]
    J --> K[認証を確認]
    K --> L[定期チェック Trigger を実行]
    L --> M[管理者に通知メールを送信]
    M --> N[管理者が登録を審査]
    N --> O[管理者がユーザーを承認または拒否]



---

## 第2段（共6段）

```markdown
### ステップ 3：質問を作成

**Questions** タブを選択し、以下の順序で質問を追加します：

| タイトル | 必須 | 検証 |
|------|------|------|
| 苗字 | ON | なし |
| Last name (English) | ON | なし |
| 名前 | ON | なし |
| First name (English) | ON | なし |
| 所属機関・部署 | ON | なし |
| **所属機関のメールアドレス** | ON | **あり** |
| 職名 | ON | なし |

![質問設定](media/image5.png)

### ステップ 4：メール検証の設定

**「所属機関のメールアドレス」** フィールドについて：

1. 右下の三点マーク（⋮）をクリック
2. **Response validation** を選択

![メール検証設定](media/image6.png)

以下のように設定します：
- **正規表現**：`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`
- **エラーメッセージ**：`Please enter a valid email address!`

![メール検証詳細](media/image7.png)

### ステップ 5：公開して URL を取得

1. フォームを **公開**
2. フォーム URL をコピーして保存
3. `.env` 設定ファイルに追加

![フォーム公開](media/image8.png)

![フォーム URL 取得](media/image9.png)

---

## Google Spreadsheet 設定

### ステップ 1：関連スプレッドシートを作成

1. フォームを Google Spreadsheet にリンク
2. スプレッドシート名を設定して作成

![スプレッドシート連携](media/image10.png)

![スプレッドシート命名](media/image11.png)

### ステップ 2：必要な列を追加

以下の列を手動でヘッダーに追加します：

| 列名 |
|------|
| **UID** |
| **AuthenticationCode** |
| **isEmailValid** |
| **isRegisted** |

![追加した列](media/image12.png)

### ステップ 3：Google Apps Script を設定

スプレッドシートのメニューからスクリプトエディタにアクセス：

![スクリプトエディタ](media/image13.png)

#### 設定変数

GAS スクリプト（`static/js/panelsearch_nanbyo/google-spreadsheet-panelsearch-nanbyo.js`）に以下の変数を設定：

| 変数 | 説明 |
|------|------|
| `PUBCASEFINDER_WEB_SERVER` | Pubcasefinder サーバー URL（例：`https://staging-pubcasefinder.dbcls.jp/`） |
| `PUBCASEFINDER_WEB_SERVER_SECRET_KEY` | サーバー側 `.env` の `GOOGLE_FORM_SECRET_KEY` と一致させる |

![サーバー設定](media/image14.png)

| 変数 | 説明 |
|------|------|
| `PBS_SPREADSHEET_ID` | スプレッドシート URL の ID（赤色部分）：`https://docs.google.com/spreadsheets/d/1kss4hHDajL0dxoeXO8qO8hpUruElcsdt_61RNMhqvFo/edit..` |
| `PBS_SPREADSHEET_DATA_NAME` | シート名：`PubCaseFinder-PanelSearch-Users-Sheet1` |

![スプレッドシート設定](media/image15.png)

| 変数 | 説明 |
|------|------|
| `COLNO_XXX` | 各列番号をスプレッドシートの位置と一致させる |
| `NOTIFICATION_MAIL_ACCOUNT` | 通知用管理者メールアドレス |

![通知設定](media/image16.png)

---

## トリガー設定

### ステップ 1：管理者メニュートリガー

管理者メニュー機能のトリガーを作成：

![管理者メニュートリガー](media/image17.png)

![メニュートリガー追加](media/image18.png)

### ステップ 2：認証

初回実行時は、認証が必要です：

![認証 1](media/image19.png)

![認証 2](media/image20.png)

![認証 3](media/image21.png)

![認証 4](media/image22.png)

### ステップ 3：フォーム送信トリガー

Google Form 送信時のトリガーを作成：

![フォーム送信トリガー](media/image23.png)

![フォーム送信トリガー設定](media/image24.png)

### ステップ 4：ステータスチェックトリガー

認証状態を定期的にチェックするトリガーを作成：

![ステータスチェックトリガー](media/image25.png)

### ステップ 5：管理者通知トリガー

管理者に通知メールを送信するトリガーを作成：

![通知トリガー](media/image26.png)

### ステップ 6：認証期限切れチェックトリガー

認証の有効期限をチェックするトリガーを作成：

![期限切れチェックトリガー](media/image27.png)

---

## 最終ステップ

✅ すべてのトリガーが設定されました  
✅ Google Form URL を記録して保存  
✅ URL を `.env` 設定ファイルに追加

```env
# .env 設定例
GOOGLE_FORM_URL=https://docs.google.com/forms/d/your-form-id/viewform
GOOGLE_FORM_SECRET_KEY=your-secret-key

---

## 第6段（共6段）

```markdown
## 付録：画像ファイルについて

本ドキュメントで参照している画像ファイル（`media/image1.png` ～ `media/image27.png`）は、元の Word 文書から抽出した画像を配置するディレクトリです。

画像を適切に配置するには：

1. 元の Word 文書（`Google-Form-PSN.docx`）から画像を抽出
2. `media/` フォルダを作成
3. 抽出した画像を `image1.png` ～ `image27.png` としてリネームして配置

または、元の Word 文書をそのまま参照することも可能です。

---

## 変更履歴

| 日付 | バージョン | 変更内容 |
|------|-----------|----------|
| 2026-08-06 | 1.0 | 初版作成（Word から Markdown へ変換） |

---

## ライセンス

本ドキュメントは Pubcasefinder プロジェクトの一部として提供されます。
