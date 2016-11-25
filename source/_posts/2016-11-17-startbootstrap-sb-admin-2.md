---
title: 使用現有 bootstrap 樣板
tags:
  - Python
  - Flask
  - App Dashboard
date: 2016-11-17 22:52:51
---


看到 [startbootstrap-sb-admin-2](https://github.com/BlackrockDigital/startbootstrap-sb-admin-2) 這漂亮而且很佛心是 free 且 open source 的樣板 (MIT license)，就把資料整合進此當做目標吧。
<!--more-->
flask-bootstrap 內有 [`bootstrap/base.html`](https://github.com/mbr/flask-bootstrap/blob/master/flask_bootstrap/templates/bootstrap/base.html)，需要把 startbootstrap-sb-admin-2 內的 code extend 且精簡。 到此步的 code 請參考 [commit #037ab3](https://github.com/ChadChang/app_dashboard/commit/037ab362f14f3157393d5bcbdf934f4c2e58ca51)

*Tips:*
1. 參考原本 base.html 的框架，分別抽出即可
2. 注意需要使用 `{% raw %}{{super()}}{% endraw %}`，避免 override 原本的設定


![整合 prototype](/images/app_dashboard_init.png)