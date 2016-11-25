---
title: App Dashboard Start
date: 2016-11-09 18:07:39
tags:
- Python
- Flask
- App Dashboard
---

打算自己做一個 App 用的 Dashboard，記錄一下 mac 環境中建置的步驟。
<!--more-->
Mac 環境中的 Python 可能有點舊，先用 homebrew 安裝 Python (需先安裝好 [Homebrew](http://brew.sh/index_zh-tw.html))
``` bash
brew install python
```

而避免與系統中的環境混淆，建議安裝  [virtualenv](https://virtualenv.pypa.io/en/stable/)
``` bash
pip install virtualenv
```

建立一個 virtual env
``` bash
virtualenv python-env

// 啟動
source python-env/bin/activate
// 離開
deactivate
```

安裝 [Flask](http://flask.pocoo.org/)
``` bash
pip install Flask 
```

依照官網 sample code 建立 hello.py

``` python hello.py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run()
```

執行 `python hello.py` 打開瀏覽器 [http://127.0.0.1:5000/](http://127.0.0.1:5000/) 就可以看到結果了

![hello flask](/images/flask_hello.png)


再來希望可以讓 Flask 讀我們的 html, 在 project 中建立 `templates` folder 並放入 `index.html` 即可讀到 index.html
``` python  hello.py
from flask import render_template
from  flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
    return render_template('index.html')

if __name__ == "__main__":
    app.run()
```

而在 debug 時可以使用 `app.run(debug = True)`，這樣在 Python code 有更動時， server 會自動 reload.


