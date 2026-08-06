# PubCaseFinder Authentication
This document describes how to add Google Authentication (OAuth 2.0) to a Flask web application.



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
* Google Account(Google Auth, Google FormとGoogle Spreadsheet作成用)
* Google SMTP送信用アカウントとアプリパスワード


