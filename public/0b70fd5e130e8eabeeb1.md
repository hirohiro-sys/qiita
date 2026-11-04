---
title: '【firebase,ts,vite】ReferenceError: process is not defined'
tags:
  - TypeScript
  - Firebase
  - エラー対処
  - vite
private: false
updated_at: '2024-08-29T20:46:43+09:00'
id: 0b70fd5e130e8eabeeb1
organization_url_name: jisou
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---
# はじめに
firebaseの初期設定でエラーが出たのでその対処法を書いていきます。

# 問題
firebaseの初期設定を終え、サーバを立ち上げると画面が真っ白になりコンソールを確認すると以下のエラーが出る。
```
Uncaught ReferenceError: process is not defined
```
以下エラー発生時の.envとfirebase.ts
```.env
REACT_APP_FIREBASE_API_KEY="XXXXX"
REACT_APP_FIREBASE_AUTH_DOMAIN="XXXXX"
REACT_APP_FIREBASE_PROJECT_ID="XXXXX"
REACT_APP_FIREBASE_STORAGE_BUCKET="XXXXX"
REACT_APP_FIREBASE_MESSAGING_SENDER_ID="XXXXX"
REACT_APP_FIREBASE_APP_ID="XXXXX"
REACT_APP_FIREBASE_MEASUREMENT_ID="XXXXX"
```
```firebase.ts
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";


const firebaseConfig = initializeApp({
  apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
  authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.REACT_APP_FIREBASE_APP_ID,
  measurementId: process.env.REACT_APP_FIREBASE_MEASUREMENT_ID
});


export const auth = getAuth(firebaseConfig);
export const db = getFirestore(firebaseConfig);
```

# 原因と解決策
環境変数の読み込み方がviteでは少し異なるため発生していたエラーでした。今回の場合だと以下の2点を修正する必要があります。supabaseを使う場合でも同じです。
- 環境変数を読み込む際はprocess.envではなくimport.meta.envを使う
- 環境変数名の前にVITEをつける

以下修正されたコードです。
```.env
VITE_REACT_APP_FIREBASE_API_KEY="XXXXX"
VITE_REACT_APP_FIREBASE_AUTH_DOMAIN="XXXXX"
VITE_REACT_APP_FIREBASE_PROJECT_ID="XXXXX"
VITE_REACT_APP_FIREBASE_STORAGE_BUCKET="XXXXX"
VITE_REACT_APP_FIREBASE_MESSAGING_SENDER_ID="XXXXX"
VITE_REACT_APP_FIREBASE_APP_ID="XXXXX"
VITE_REACT_APP_FIREBASE_MEASUREMENT_ID="XXXXX"
```
```firebase.ts
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";


const firebaseConfig = initializeApp({
  apiKey: import.meta.env.VITE_REACT_APP_FIREBASE_API_KEY,
  authDomain: import.meta.env.VITE_REACT_APP_FIREBASE_AUTH_DOMAIN,
  projectId: import.meta.env.VITE_REACT_APP_FIREBASE_PROJECT_ID,
  storageBucket: import.meta.env.VITE_REACT_APP_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: import.meta.env.VITE_REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
  appId: import.meta.env.VITE_REACT_APP_FIREBASE_APP_ID,
  measurementId: import.meta.env.VITE_REACT_APP_FIREBASE_MEASUREMENT_ID
});


export const auth = getAuth(firebaseConfig);
export const db = getFirestore(firebaseConfig);
```
これで正常に表示されるようになりました。

:::note warn
補足
もしgithub acitonsなどでデプロイしている場合はgithubに登録している環境変数名とcicdのymlファイル内の環境変数名も修正してあげないと環境変数が読み込まれないので注意です。
:::


# おわりに
vite環境でjestを使った際はimport.metaを使えなかった記憶があったのですが、今回はなぜかテストが通ったのでこのままで行こうと思います。

https://qiita.com/bisketoriba/items/93163a95eac46802e297
# 参考
https://stackoverflow.com/questions/76090901/uncaught-referenceerror-process-is-not-defined-in-react-npm-run-dev-using-vit

https://vitejs.dev/guide/env-and-mode.html
