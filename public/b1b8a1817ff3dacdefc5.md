---
title: >-
  【NestJS】GCSに画像をアップロードするとPayloadTooLargeError: request entity too
  largeで弾かれたので解決した件
tags:
  - GCS
  - NestJS
private: false
updated_at: '2022-03-27T01:08:31+09:00'
id: b1b8a1817ff3dacdefc5
organization_url_name: boomtechcafe
slide: false
ignorePublish: false
---
# 概要

サイズの大きい画像を送信すると`PayloadTooLargeError: request entity too large` エラーーが発生したので解決方法をまとめる

https://stackoverflow.com/questions/50101512/http-413-request-entity-too-large-in-node-js-project-in-gae

# 解決方法

`bodyParser`を利用してリクエストバッファサイズを上げることが必要だったみたい

https://www.npmjs.com/package/body-parser

```diff_typescript:src/main.ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
    logger: loggerFactory.useFactory('warn'),
  })
  const logger = app.get<CustomLogger>(CustomLogger)
+  app.use(bodyParser.json({ limit: '32mb' }))
+  app.use(bodyParser.urlencoded({ limit: '32mb', extended: true }))
});
```
