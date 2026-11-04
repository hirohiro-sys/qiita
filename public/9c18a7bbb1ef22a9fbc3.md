---
title: Next.jsで急にcssがあたらなくなった
tags:
  - CSS
  - Next.js
private: false
updated_at: '2025-06-23T12:43:29+09:00'
id: 9c18a7bbb1ef22a9fbc3
organization_url_name: jisou
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---
# 問題
Next.jsを使って個人アプリをいじっていたところ、急にcssが当たらなくなってしまったので解決策を書きます。

# 試したこと
- スーパーリロード
- ブラウザのcookieなどを消す
- デプロイしたものはcssが当たっている
- node_modulesと.nextを入れ直す

# 解決策
結局node_modulesと.nextを入れ直すことでなおりました。2つまとめて消しましたが多分node_modulesか.nextのどちらかだけでもなおる気がするので、まずはnode_modulesだけ入れ直してみると良さそうです。
```
rm -rf node_modules .next
npm install
```

久々に触ったプロジェクトで全体的に少し重かったのと、直前にプロジェクト自体のディレクトリ配置を変えていたので、キャッシュに問題があったのかと思っています。

ざっとググってもそれっぽいものは出てこなかったのですが、perplexityで色々探索したところ以下の記事でも同じようなことが書かれていました。

https://qiita.com/bearl27/items/4099f6d9bf7e433b2537

# 追記
その後なぜかリポジトリが壊れたので、cloneしなおすのもありかもです。
