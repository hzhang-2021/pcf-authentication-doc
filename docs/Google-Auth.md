# OAuth 2.0 client credentials issued by Google, used for the application's login feature.

[参照文書](https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja)
[参照文書](https://developers.google.com/identity/gsi/web/guides/overview?authuser=1)

# 例として、下のサーバ用OAuth 2.0 credentialsの作成を説明する。
 「https://staging-pubcasefinder.dbcls.jp」

## Prerequisites
- Domain Name of the Flask Server(例：https://staging-pubcasefinder.dbcls.jp)
- Google Account(Google Credential作成用)

## 1. Projectを新規作成
ご利用Google AccountでGoogleをログインして、[Google API Console](https://console.developers.google.com/apis?authuser=1)へアクセス

![](../images/Google-Auth-1.png)

「プロジェクトの選択」ブタンをクリックする

![](../images/Google-Auth-2.png)

「NEW PROJECT」ブタンをクリックする

![](../images/Google-Auth-3.png)
「project name」に任意の名前を入力し、「CREATE」ボタンをクリックする

![](../images/Google-Auth-4.png)

上に作成したプロジェクトを確認する。



## 2. Authentication consentの設定
![](../images/Google-Auth-4.png)
左側「API & Services」リストに「OAuth consent screen」をクリックする。

「外部」を選択し、「作成」ボタンをクリックする。

「アプリ登録の編集」画面へ遷移する。

![](../images/Google-Auth-5.png)

「アプリ名」に名前を決めて入力。

「ユーザーサポートメール」に自分メールを選択する

したにスクロールする

![](../images/Google-Auth-6.png)

「アプリケーションのホームページ」にサーバーのURLを入力する。

「application privacy policy link」と「Application terms of service link」に各URLを入力

「+ドメインの追加」ボタンクリックし、ドメイン名を入力する。

「デベロッパーの連絡先情報」に管理者のメールアドレスを入力する。

「保存して次へ」ボタンをクリックし、「スコープ」編集画面へ遷移する。

![](../images/Google-Auth-7.png)

「スコープを追加または削除」ボタンをクリック

![](../images/Google-Auth-8.png)

「.../auth/userinfo.email」、「.../auth/userinfo.profile」、「openid」を選択する。

「更新」ボタンをクリックする。

![](../images/Google-Auth-9.png)

「保存して次へ」ボタンをクリックする。

![](../images/Google-Auth-10.png)

登録した内容は確認して「ダッシュボードに戻る」ボタンをクリック



## 3. Credentials(Client ID/Secret)を作成する

![](../images/Google-Auth-11.png)

「認証情報」をクリック

![](../images/Google-Auth-12.png)

「認証情報の作成」をクリック

![](../images/Google-Auth-13.png)

「OAuthクライアントID」をクリック

![](../images/Google-Auth-14.png)

「アプリケーションの種類」に「ウェブアプリケーション」をクリック

![](../images/Google-Auth-15.png)

「Name」に任意の名前を入力

「Authorized JavaScript orgins」と「Authorized redirect URIs」にサーバURIを設定し「作成」ボタンをクリック。
 この「Authorized redirect URIs」は、認証成功した、Flask側Callback先となっています

![](../images/Google-Auth-16.png)

ここで、"client_id"と"client_secret_key"を作成する。

## 4. APPをPublishingする
![](../images/Google-Auth-17.png)

「OAuth consent screen」を選択。

Publishing
statusはTestingになっています。外部ユーザー利用できるため、「PUBLISH APP」ブタンをクリック

![](../images/Google-Auth-18.png)

「CONFIRM」する
![](../images/Google-Auth-19.png)

Publishing statusは現在、「in production」になります。それで、Google側の認証サービスは準備できました。

