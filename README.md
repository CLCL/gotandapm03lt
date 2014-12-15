# SyndicationがSynじられない

2014-12-17 大野義貴

---

# 自己紹介

* 大野 義貴（CL）
* 五反田にある会社のWebのインフラ側作業者
* 社名が言えないのはお察しください
* 会社はPHPしか使えない

---

# RSS Feed = WebAPIの一種

* Web Syndication用

![web](http://upload.wikimedia.org/wikipedia/en/4/43/Feed-icon.svg)

---

# RSS Feedから他のWebサイトに差し込み使用

* Syndication!
* 差し込み方法
  - JavaScript？（DOM再描画ださい）
  - SSI？（化石すぎて誰も知らない）
  - PHP？（ナウい）
* PHPを採用

--- 

# ところでWordPressのRSS

* いつの間にかWordPress自体がRSS生成器に
  - WordPress界の鬼Hackヤバイ
    - 正直めんどくね？（MovableType使ってた俺的に）
* WordPress（PHP）が「動的」にRSS
  - 動的？（MovableType使ってた俺的に）

---

# WordPressの動的RSSは12時間更新されない

* いったんRSSを配信済みのクライアントには12時間内だとリロードでも304を返す
  - Cache-Control:とか無視するタイプ
  - If-Modified-Since:とか見るタイプ
* APIとしてはひどい

---

# WordPressから最新のRSSを読みだすHack

* どっち？
  - WordPressのRSSキャッシュを12時間から5分とかに変更
  - WordPressのRSSをフェッチして差し込み用HTML生成するプログラムでリクエストヘッダ付与をコメントアウト

![ジャガーノート](http://blog-imgs-47-origin.fc2.com/d/e/s/deserlife/jg.jpg)

* 後者を採用

---

# 結果：ページが表示されなくて修羅場

* ページの全てのリクエストで差し込み実行
* ページの全てのリクエストでHTTP経由でWordPress RSS取得
* ページの全てのリクエストでWordPressがMySQL稼働させエントリをRSSに加工して200で送信
* 全てのリクエストごとにApacheの接続利用
* →[error] server reached MaxClients setting

```http://localhost/server-status
Apache Server Status on localhost

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

* **理屈わからないでWebAPIのように使うな**

# 教訓（教育訓練の略な）

* HTTPのリクエスト・レスポンスを覚えよう←（ｲﾏｺｺ）
* ブラウザのキャッシュを活用しよう←（1か月後）
* RSSを静的ファイルにすると、ふかがひくくなるぞ！←（2か月後）
* Nginxってしってる？←（3か月後）
* HTTPサーバのキャッシュをつかってみよう←（4か月後）
* Memcachedってしってる？←（5か月後）
* ええっ！XAMPPってMemcachedはつかえないのかい？←（6か月後）
* XAMPPはやめてVagrantにしよう←（7か月後）

