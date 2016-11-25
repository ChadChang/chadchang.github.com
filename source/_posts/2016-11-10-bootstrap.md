---
title: 加入 bootstrap
tags:
  - Python
  - Flask
  - App Dashboard
date: 2016-11-10 20:55:02
---


嘗試用 [Bootstrap](http://getbootstrap.com/) 來當整個 project 的網頁部分
<!--more-->

首先安裝 [flask-bootstrap](https://pythonhosted.org/Flask-Bootstrap/) 來建立最小可以執行的 bootstrap web 畫面 


html 部分會放在 `/tempalate` 底下，而因 template 的變動不會自動 reload，必須重新啟動 server，只需增加 `app.config['TEMPLATES_AUTO_RELOAD'] = True` 即可。
 

``` bash
pip install flask-bootstrap
```

``` python app.py
from flask import Flask,render_template
from flask_bootstrap  import Bootstrap

app = Flask(__name__)
app.config['TEMPLATES_AUTO_RELOAD'] = True
bootstrap = Bootstrap(app)

@app.route('/')
def index():
    return render_template('index.html')

if __name__ == "__main__":
    app.run(debug = True)
```



建立整個網站的模版 `base.html` 

``` html base.html
{% extends "bootstrap/base.html"  %}

{% block title %}App Dashboard{% endblock %}

{% block navbar %}
<div class="navbar navbar-inverse" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="/">App Dashboard</a>
        </div>
        <div class="navbar-collapse collapse">
            <ul class="nav navbar-nav">
                <li><a href="/">Home</a></li>
            </ul>
        </div>
    </div>
</div>
{% endblock %}

{% block content %}
<div class="container">
  {% block page_content %}{% endblock %}
</div>
{% endblock %}
```

其他每個畫面只要 extends base.html 即可。

``` html index.html
{% extends "base.html" %}

{% block title %}Main{% endblock %}

{% block page_content %}
<h1>Main</h1>
{% endblock %}
```

結果如下:
![flask-bootstrap](/images/flask-bootstrap.png)

