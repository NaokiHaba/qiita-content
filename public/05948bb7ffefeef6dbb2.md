---
title: 【TypeScript + NestJS】crypto-jsで暗号化した文字列を複合化するとMalformed UTF-8 data になった件
tags:
  - npm
  - TypeScript
  - crypto
  - NestJS
private: false
updated_at: '2022-06-02T19:05:15+09:00'
id: 05948bb7ffefeef6dbb2
organization_url_name: boomtechcafe
slide: false
ignorePublish: false
---
# 概要

暗号化した文字列をURLパラメータにセットしてバックエンドと送受信すると以下のエラーが発生した

この記事はエラーの解消までのプロセスを残すものです

https://www.npmjs.com/package/crypto-js

```bash
error    Malformed UTF-8 data
```


# 環境

- npm 6.14.13
- typescript ^4.2.3
- crypto-js 4.1.1


# 本題

GitHubで説明されている通りに実装してみたが冒頭のエラーが発生していました。


https://github.com/brix/crypto-js

```ts
var CryptoJS = require("crypto-js");

// Encrypt
var ciphertext = CryptoJS.AES.encrypt('my message', 'secret key 123').toString();

// Decrypt
var bytes  = CryptoJS.AES.decrypt(ciphertext, 'secret key 123');
var originalText = bytes.toString(CryptoJS.enc.Utf8);

console.log(originalText); // 'my message'
```

## 解消方法

ポイントは複合用の秘密鍵を`Latin1` でエンコードする必要があることでした

データが`ISO-8859-1` でエンコードされていたことから`UTF-8`では`parse`できないよ！！

とのことです。。

ちなみに`Issues`にも上がっていたが紹介されている方法では解決しなかったので、今回の解消方法をコメントしておきました。

https://github.com/brix/crypto-js/issues/271


### 解決したコード 

```ts:src/utilities/CustomCryptoJs.ts
public getEncodePath(decodePath: string): string {
    const json = CryptoJS.AES.encrypt(
        JSON.stringify(decodePath),
        this.SECRET_KEY
    ).toString()

    return CryptoJS.enc.Base64.stringify(CryptoJS.enc.Latin1.parse(json))
}

public getDecodePath(encodePath: string): string {
    const decodePath = decodeURIComponent(encodePath)

    const data = CryptoJS.enc.Base64.parse(decodePath.toString()).toString(
        CryptoJS.enc.Latin1
    )

    const bytes = CryptoJS.AES.decrypt(data, this.SECRET_KEY).toString(
        CryptoJS.enc.Utf8
    )

    return JSON.parse(bytes)
}
```
