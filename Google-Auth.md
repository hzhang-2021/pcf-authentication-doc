**Sign In with Google for Web**

[参照文書](https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja)

Googleのアカウントを使ってログインできる仕組み

事前に[Google API
Console](https://console.developers.google.com/apis?authuser=1)で認証用のAPIを発行し、Domain名、認証済みのRedirect
URIとユーザーアクセススコープとテスト用アカウントなど設定し、"client_id"と"client_secret_key"を作成して、uwsgi(app.py)サーバに保存しておく。

1.  ユーザーがクリックなどの動作で、Google認証を発火する

2.  uwsgi側、ユーザーのrequestをもらって、python package
    authlibの機能を利用して、"client_id"と"client_secret_key"を設定し、Google認証サーバにユーザー認証を転送する。

3.  Google認証サーバが直接ユーザーへログインのPromptを返す。

4.  ユーザーとGoogle認証サーバーが認証やりとり。

5.  認証済み、Googleから、事前に設定したuwsgiのURIへCallback、ユーザーのaccess
    tokenを返す。

6.  uwsgi側が、返したaccess
    tokenを利用して、Google認証サーバーへユーザー情報(メール)をRequestする。ここで、要求できる項目の範囲は、ユーザーアクセススコープで定義する。

7.  Google認証サーバーがユーザー情報(メール)を返す

8.  uwsgi側、ユーザー情報をもらって、各動作が出来ます。

下に、例として、「https://staging-pubcasefinder.dbcls.jp」サーバ用Google
Authの作成を説明する。

1.  Google API Consoleの設定

> [参照文書](https://developers.google.com/identity/protocols/oauth2/web-server?hl=ja)、[参照文書](https://developers.google.com/identity/gsi/web/guides/overview?authuser=1)

ご利用Google AccountでGoogleをログインして、　[Google API
Console](https://console.developers.google.com/apis?authuser=1)へアクセス

![](media/image1.png){width="7.268055555555556in"
height="2.7847222222222223in"}

「プロジェクトの選択」ブタンをクリックする

![](media/image2.png){width="7.268055555555556in"
height="5.134722222222222in"}

「NEW PROJECT」ブタンをクリックする

![](media/image3.png){width="7.268055555555556in"
height="3.1041666666666665in"}「project
name」に任意の名前を入力し、「CREATE」ボタンをクリックする

![](media/image4.png){width="7.268055555555556in"
height="5.134722222222222in"}

上に作成したプロジェクトを確認する。

左側「API & Services」リストに「OAuth consent screen」をクリックする。

「外部」を選択し、「作成」ボタンをクリックする。

「アプリ登録の編集」画面へ遷移する。

![](media/image5.png){width="7.268055555555556in"
height="5.527777777777778in"}

「アプリ名」に名前を決めて入力。

「ユーザーサポートメール」に自分メールを選択する

したにスクロールする

![](media/image6.png){width="7.268055555555556in"
height="5.490277777777778in"}

「アプリケーションのホームページ」にサーバーのURLを入力する。

「application privacy policy link」と「Application terms of service
link」に各URLを入力

「+ドメインの追加」ボタンクリックし、ドメイン名を入力する。

「デベロッパーの連絡先情報」に管理者のメールアドレスを入力する。

「保存して次へ」ボタンをクリックし、「スコープ」編集画面へ遷移する。

![](media/image7.png){width="7.268055555555556in"
height="5.490277777777778in"}

「スコープを追加または削除」ボタンをクリック

![](media/image8.png){width="7.268055555555556in" height="6.30625in"}

「.../auth/userinfo.email」、「.../auth/userinfo.profile」、「openid」を選択する。

「更新」ボタンをクリックする。

![](media/image9.png){width="7.268055555555556in" height="6.30625in"}

「保存して次へ」ボタンをクリックする。

![](media/image10.png){width="7.268055555555556in"
height="7.8597222222222225in"}

登録した内容は確認して「ダッシュボードに戻る」ボタンをクリック

![](media/image11.png){width="7.268055555555556in"
height="3.298611111111111in"}

「認証情報」をクリック

![](media/image12.png){width="7.268055555555556in"
height="4.1506944444444445in"}

「認証情報の作成」をクリック

![](media/image13.png){width="7.268055555555556in"
height="4.1506944444444445in"}

「OAuthクライアントID」をクリック

![](media/image14.png){width="7.268055555555556in"
height="4.1506944444444445in"}

「アプリケーションの種類」に「ウェブアプリケーション」をクリック

![](media/image15.png){width="7.268055555555556in"
height="6.279861111111111in"}

「Name」に任意の名前を入力

「Authorized JavaScript orgins」と「Authorized redirect
URIs」にサーバURIを設定し「作成」ボタンをクリック。

![](media/image16.png){width="7.268055555555556in"
height="6.279861111111111in"}

ここで、"client_id"と"client_secret_key"を作成する。

![](media/image17.png){width="7.268055555555556in"
height="6.279861111111111in"}

「OAuth consent screen」を選択。

Publishing
statusはTestingになっています。外部ユーザー利用できるため、「PUBLISH
APP」ブタンをクリック

![](media/image18.png){width="7.268055555555556in"
height="6.279861111111111in"}

「CONFIRM」する

![](media/image19.png){width="7.268055555555556in"
height="6.279861111111111in"}

Publishing statusは現在、「in
production」になります。それで、Google側の認証サービスは準備できました。

