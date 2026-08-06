# 一、Sign Up With Google 仕組み

![](images/Google-Form-Common-0.PNG)
1.  Userが「Sign Up」をスタート

2.  Flask側、Configファイルに定義されたGoogle Form URLへRedirect

3.  Google Formをユーザー側へ帰す

4.  ユーザーが名前、メールアドレス、Affiliation,JobTitleなど入力して、Submitする

5.  Google
    Form側、ユーザー入力Responseを新規作成し、情報をGoogleFormのResponseListとGoogle
    SpreadsheetのSheet、二箇所に保存して、Google Formの「onSubmit Trigger」とGoogleSpreadsheetの「onSubmit Trigger」を自動的に呼び出す。
    Google Formの「onSubmit Trigger」は、このResponseのResponseIDをGoogle Spreadsheet該当行に保存

6.  GoogleSpreadsheetのSubmit
    Triggerを実行、Pubcasefinderサーバへユーザー新規追加を投げて

7.  Pubcasefinderサーバのインタフェースです。新ユーザーをMySQLのuser_accountテーブルへ登録、AuthenticationCodeを作成して、ユーザーのメールアドレスへAuthenticationメールを送信

8.  ユーザー側、Authenticationメール中のリンクをクリックして、Authenticationを行います。

9.  Pubcasefinderサーバで、ユーザーのAuthenticationを受けて、Account_authテーブルへ登録。ユーザーのメールアドレスへ登録成功のNotificationメールを送信する。

10. GoogleSpreadsheetのStatus Check
    Triggerを実行、定期的に、PubcasefinderサーバへAuthentication完了するかを確認する。

11. Pubcasefinderサーバで、ユーザーのAuthentication完了、「承認された」、「退会」などの状態変更を記録すする

12. ユーザーがPubcasefinderのWeb上、「退会」を行い

13. 10番のstatus check
    Triggerの実行で、Pubcasefinderサーバから、「退会」したユーザーのResponseIDでGoogleFormの該当ユーザーのResponseを削除する


# 二、例として、下のサーバのGoogleFormの作成を説明する。
「https://staging-pubcasefinder.dbcls.jp」



## 1. Google Formを新規作成

ご利用のGoogle AccountでログインGoogleへアクセス
![](images/Google-Form-Common-1.png)  
「Forms」というGoogle APPをクリックする


![](images/Google-Form-Common-2.png)  
「Start a new form」をクリックする


## 2. Google Formの設定

![](images/Google-Form-Common-3.png)  
「Settings」タブを選択し、「Responses」部分を設定する。
⁂「Make this a quiz」部分は押さないで



![](images/Google-Form-Common-4.png)  
「Presentation」部分を設定する。特に、Confirmation message部分は編集する。



## 3. GoogleFormのQuestionの作成

![](images/Google-Form-Common-5.png)  
「Questions」タブを選択、FormのTitleを編集、Formの各Questionを以下の順番で追加する。
| Title | Required | Validation |
|-------|:--------:|:----------:|
| Last name (English) | ON | NO |
| Last name (native language) | OFF | NO |
| First name (English) | ON | NO |
| First name (native language) | OFF | NO |
| Affiliation (English) | ON | NO |
| Affiliation (native language) | OFF | NO |
| Affiliation email | ON | YES |
| Job title (English) | ON | NO |
| Job title (native language) | OFF | NO |


![](images/Google-Form-Common-6.png)     
特に、「Affiliation email」部分は、下右の三点マックよりResponse validationを設定する。
- Regular expression
- Matches
- ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
- Please enter a valid email address!




## 4.Google Form のPublish

![](images/Google-Form-Common-7.png)  
FormをPublishする。




## 5. GoogleFormのLink

![](images/Google-Form-Common-8.png)  
このFormのURLを表示して、保存する。「.env」ファイルのGOOGLE_FORM_URL_PCFへ設定する。


## 6.GoogleFormの「onSubmit Trigger」の作成

Google Form側、新規登録くる時、Google はユーザー入力した情報より、Responseを新規作成し、GoogleFormのResponseListとGoogleSpreadsheetのSheetに追加した後、GooleFormとGoogleSpreadsheetの「on Form Submit Trigger」を行う

ここは、GoogleFormの「onSubmit Trigger」を作成する。このTriggerは、該当ResponseのResponseIDをGoogleSpreadsheetのResponseID Columnへ保存する処理を行う。


![](images/Google-Form-Common-9.png)  
図面のように、GoogleFormの編集画面を開けて、「google form id」を記録し、GAS編集画面を開ける



![](images/Google-Form-Common-10.png)  
GASに、GithubにPUSHした「static/js/google-form-common.js」で、Scriptを設定する。Google
SpreadsheetのID、SpreadsheetのSheet名を設定する。COLNOの部分は、SpreadsheetのColumnの順番を一致する。保存。



![](images/Google-Form-Common-11.png)  
onSubmit Triggerを追加




# 三、Google Spreadsheetの作成

## 1. Google Form Linked Google Spreadsheetを新規作成
![](images/Google-Form-Common-12.png)  
Formへ関連するGoogle Spreadsheetを設定する。


![](images/Google-Form-Common-13.png)  
Spreadsheetの名前を設定して、作成する



## 2. Columnを新規追加
Spreadsheetに自動的作成したColumnの後ろ、追加ColumnのHeaderを設定する。
![](images/Google-Form-Common-14.png)  

ColumnのHeaderへ、以下のColumnを手動で追加する
- UID
- AuthenticationCode
- isEmailValid
- isRegisted
- ResponseID



## 3. Google Spreadsheet のGASを編集する
![](images/Google-Form-Common-15.png)  

Spreadsheetの機能を作ります。



![](images/Google-Form-Common-16.png)  
GASに、GithubにPUSHした「static/js/google-spreadsheet-common.js」で、Scriptを設定する。
Codeの中身を確認する：

- PUBCASEFINDER_WEB_SERVER:
  PubcasefinderサーバのURL(例: https://staging-pubcasefinder.dbcls.jp/)を設定する。

- PUBCASEFINDER_WEB_SERVER_SECRET_KEY:

   Pubcasefinderサーバー側「.env」に「GOOGLE_FORM_SECRET_KEY」の値と一致

![](images/Google-Form-Common-17.png)  
- PBS_SPREADSHEET_ID： \
  例：以下の[]部分 \
  https://docs.google.com/spreadsheets/d/**[1evhGHU-Kpfhtm94DCL0KHMN5ljHEV2rRnrCHCy8B_WQ]**/edit..

- PBS_SPREADSHEET_DATA_NAME：
  Spreadsheetのsheetの名前を「PubCaseFinder-Users-Sheet1」に設定する

- PBS_GOOGLEFORM_ID：
  二の6に記録したgoogle form id をここに設定する

CodeのCOLNOの部分は、Spreadsheetの各Columnの順番を一致する


## 4. Triggerを設定する

![](images/Google-Form-Common-18.png)  
Triggerを設定する。

### 「onOpen Trigger」: GoogleSpreadsheetの画面にMENUを追加する用
![](images/Google-Form-Common-19.png)  

![](images/Google-Form-Common-20.png)  

初めて場合、認証を行います。
![](images/Google-Form-Common-21.png)  

![](images/Google-Form-Common-22.png)  

![](images/Google-Form-Common-23.png)  

![](images/Google-Form-Common-24.png)  

Menu用Triggerを作成しました。

### 「on Form Submit Trigger」の作成

Google Form側、新規登録きっだ場合、データは

![](images/Google-Form-Common-25.png)  

![](images/Google-Form-Common-26.png)  

### 「Check Status Trigger」を追加する

このTriggerは、定期的に、Pubcasefinderのサーバから、ユーザーの状態変更を取って、処理を行う。

退会したユーザーは、Google FormのReponseListから、該当Responseを削除処理を行う

![](images/Google-Form-Common-27.png)  

Check Status Triggerを追加. 「Select type of time based trigger」と「Select hour interval」部分は、SpreadsheetからPubcasefinderのサーバへアクセスし、状態変更したのユーザーを取る処理の頻度を定義する。ご自由にお使いください。


### 「Authentication Expiration Check Trigger」を作成

![](images/Google-Form-Common-28.png)  

