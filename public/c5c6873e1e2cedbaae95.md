---
title: 'Error: parameter is required (check serviceDomain and apiKey)'
tags:
  - エラー対処
private: false
updated_at: '2024-10-13T21:15:00+09:00'
id: c5c6873e1e2cedbaae95
organization_url_name: jisou
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---
# はじめに
github actionsを使ったvercelデプロイ時にビルドの箇所でエラーが出たので、解決策を書きます。
# 問題
ローカルでのビルド成功するが、cicdのビルドステップではエラーが出る。
```
Error: parameter is required (check serviceDomain and apiKey)
```
エラー時のyml
```yml
name: Github Auto Deploy

on: push

env:
  VERCEL_ORG_ID: ${{ secrets.ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.PROJECT_ID }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install Vercel CLI
        run: npm install --global vercel@latest

      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}

      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}

```
# 解決策
ymlに環境変数を指定してあげれば直りました。正直エラー文に何が問題か書いてありますし、cicd時のみのエラーなので少し考えればわかりそうですが、vercelに環境変数を設定すればPROJECT_IDとかから勝手にgithub actions側で読み込んでくれると思い込んでいたためymlで指定していませんでした。(思い込み怖い)

```yml
name: Github Auto Deploy

on: push

env:
  VERCEL_ORG_ID: ${{ secrets.ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.PROJECT_ID }}
  # 環境変数を指定してあげる
  API_KEY: ${{ secrets.API_KEY }}
  SERVICE_DOMAIN: ${{ secrets.SERVICE_DOMAIN }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Install Vercel CLI
        run: npm install --global vercel@latest

      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}

      - name: Build Project Artifacts
        run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}

      - name: Deploy Project Artifacts to Vercel
        run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
```
ビルド時に環境変数を渡してあげることで外部のapiとの通信が可能になります。(今回の場合はmicrocmsのapi)
# おわりに
vercel使いやすいです。
