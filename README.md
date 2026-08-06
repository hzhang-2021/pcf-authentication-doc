# PubCaseFinder Authentication
This document describes how to add Google Authentication (OAuth 2.0) to PubCaseFinder Flask Server.



# Overview

Google Authentication allows users to sign in using their Google accounts instead of creating a separate username and password.

The authentication flow is as follows:

```text
+--------+        +----------------+        +----------------+
| Browser| -----> | Flask Server   | -----> | Google OAuth   |
+--------+        +----------------+        +----------------+
      ^                                          |
      |                                          |
      +------------------------------------------+
                Authentication Result
```





# Prerequisites
* Domain Name of the Flask Server(例：https://staging-pubcasefinder.dbcls.jp)
* Google Account(Google Auth credentials, Google FormとSpreadsheet作成用)
* Google SMTP送信用アカウントとアプリパスワード




\
\
# 作成:
## 一、Google OAuth 2.0 Client ID/Secretの作成
For more information, see the [Google Authentication Guide](Google-Auth.md). \
The results will be used at the following configuration items:
- GOOGLE_CLIENT_ID
- GOOGLE_CLIENT_SECRET





## 二、共通用版ログイン機能用Google FormとSpreadsheetの作成
For more information, see the [Google Form/Spreadsheet(共通用版)](Google-Form-Common.md) \
The results will be used at the following configuration items:
- GOOGLE_FORM_SECRET_KEY
- GOOGLE_FORM_URL_PCF




## 三、Panelsearch(Nanybo)ログイン機能用Google FormとSpreadsheetの作成
For more information, see the[Google Form/Spreadsheet(Panelsearch(Nanybo)版)](Google-Form-PSN.docx) \
The results will be used at the following configuration items:
- GOOGLE_FORM_URL_PSN


## 四、Google SMTP送信用アカウントとアプリパスワード
[Google SMTP送信用アカウントとアプリパスワード](Gmail-SMTP.pptx)




