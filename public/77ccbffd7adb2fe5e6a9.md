---
title: 【TypeScript】 ts-node で Unknown file extension ".ts" エラーが出るとき
tags:
  - Node.js
  - TypeScript
private: false
updated_at: '2022-05-05T18:32:43+09:00'
id: 77ccbffd7adb2fe5e6a9
organization_url_name: boomtechcafe
slide: false
ignorePublish: false
---
# 概要

`ts-node` で実行すると以下のエラーが出る場合の対処方法を残す

```bash
ts-node src/flow.ts                                                                           1 ✘  18:13:01  
TypeError [ERR_UNKNOWN_FILE_EXTENSION]: Unknown file extension ".ts" xxxx
```

https://github.com/TypeStrong/ts-node/issues/1062

# やり方

`"type": "module"`を`package.json`から削除することで実行することができるようになる

```diff_json:package.json
{
-  "type": "module",
  "name": "practice",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```
