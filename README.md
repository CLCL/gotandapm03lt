# SyndicationがSynじられない

†Gotanda.pm #3 LT†

2014-12-17 大野義貴(CL)

---

# 自己紹介

* 大野義貴（CL) twitter @CLCLCL
* CPAN Author流行ってた頃ノリでCPAN Author
* 前職は印刷業のエンジニア
* 印刷見積をWebAPI化する雰囲気醸せる前に退職
* 現職は五反田の派遣・アポイントマッチング会社のサーバエンジニア
* 社名が言えないのはお察しください
* 会社はPHPしか使わない

---

# RSS Feed = WebAPIの一種

![web](http://upload.wikimedia.org/wikipedia/en/4/43/Feed-icon.svg)

* Web Syndication用

---

# RSS Feedから他のWebサイトに差し込み使用

* Syndication!
* 差し込み方法
  1. JavaScript？（DOM再描画ダサい）
  1. SSI？（化石すぎて誰も知らない）
  1. CGI?（当然のごとく知っている人がいない）
  1. PHP？（ナウい）
* PHPを採用

--- 

# ところでWordPressのRSS

* いつの間にかWordPress自体がRSS生成器に
* WordPress界の鬼Hackヤバイ
* 正直めんどくね？（MovableType派的に）
* WordPress（PHP）が“動的”にRSS生成
* 動的？（MovableType使いの素朴な疑問）

---

# WordPressの動的RSSは12時間更新されない

* いったんRSSを配信済みのクライアントには12時間以内だとリロードでも304を返す
* Cache-Controlとか無視するタイプ
* If-Midified-Sinceとか見るタイプ
* APIとしてはひどい

---

# WordPressから最新のRSSを読みだすHack

* どっち？
* WordPressのRSSキャッシュを12時間から5分とかに変更
* WordPressのRSSをフェッチするプログラム側でリクエストヘッダのIf-Modified-Since等の2回目アクセスにつけるヘッダを除去し、毎回初回アクセスに見せかける
* →後者を選択！

![ジャガーノート](http://blog-imgs-47-origin.fc2.com/d/e/s/deserlife/jg.jpg)

---

# 結果：ページが表示されなくなって現場爆発

* ページの全てのリクエストで差し込み実行
* ページの全てのリクエストでHTTP経由でWordPress RSS取得
* ページの全てのリクエストでWordPressがMySQL稼働させエントリをRSSに加工して200で送信
* 全てのリクエストごとにApacheの接続利用
* →[error] server reached MaxClients setting

```http://localhost/server-status
Apache Server Status on localhost

WWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
WWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
WWWWWWWWWWWWWWWWWWWWWWWWWWWWWW
WWWWWWWWWWWWWWWWWWWWWWWWWWWWW.

Scoreboard Key:
"_" Waiting for Connection, "S" Starting up, "R" Reading Request,
"W" Sending Reply, "K" Keepalive (read), "D" DNS Lookup,
"C" Closing connection, "L" Logging, "G" Gracefully finishing,
"I" Idle cleanup of worker, "." Open slot with no current process
```

---

# 教訓

* **理屈わからないでWebAPI使うな**
* HTTPのリクエスト・レスポンスって結構考えられているので基本従おう（RESTもそうだよね）
* 自分でWebAPIを実装するなら304レスポンスとか使わない手はないぞ！
