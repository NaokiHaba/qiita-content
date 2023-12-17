---
title: TypeScriptで定数オブジェクトを使いまわす方法
tags:
  - TypeScript
private: false
updated_at: '2022-05-05T22:43:06+09:00'
id: dbabf9d2be2f37032c5a
organization_url_name: null
slide: false
ignorePublish: false
---
共通で使用する定数を読み取り専用・データ型として使い回したかったので作ってみました

```ts
export const HTTP_STATUS_CODES = {
  OK: 200,
  CREATED: 201,
  UNAUTHORIZED: 401,
  FORBIDDEN: 403,
  NOT_FOUND: 404,
  BAD_REQUEST: 400,
}
```

## やりかた

```ts
export const HTTP_STATUS_CODES = {
  OK: 200,
  CREATED: 201,
  UNAUTHORIZED: 401,
  FORBIDDEN: 403,
  NOT_FOUND: 404,
  BAD_REQUEST: 400,
} as const

export type HttpStatus =
  typeof HTTP_STATUS_CODES[keyof typeof HTTP_STATUS_CODES]
```

## 説明

### as constとは
> オブジェクトリテラルの末尾にas constを記述すればプロパティがreadonlyでリテラルタイプで指定した物と同等の扱いになります。

[constアサーション「as const」 (const assertion)](https://book.yyts.org/reference/values-types-variables/const-assertion)

例えば、定数オブジェクトの値を変更しようとすると、読み取り専用だから書き換えできないよ！！って怒ってくれます

constは言わずもがな、再代入、再宣言不可の概念通りの実装になるので、思わぬバグを防げます。


```ts
HTTP_STATUS_CODES.OK = 204
// Cannot assign to 'OK' because it is a read-only property.
```
![スクリーンショット 2021-11-17 3.41.30.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/1344e8b7-56ac-e219-bdfa-1393cae6c98a.png)

### export typeとは
> オブジェクトの型を取得します

### typeof とは
> オブジェクトからデータ型を生成します

```ts
typeof HTTP_STATUS_CODES

/*
  OK: number,
  CREATED: number,
  UNAUTHORIZED: number,
  FORBIDDEN: number,
  NOT_FOUND: number,
  BAD_REQUEST: number,
*/
```

### key ofとは
> 型のプロパティ名のUnion型で返します

```ts
type HttpStatus = 200 | 201 | 401 | 403 | 404 | 400
```

### keyof typeof

> T[K]はindexed access types、またはlookup typesと呼ばれ、オブジェクトのプロパティにアクセスするような記法でプロパティの型を取得できます。

超わかりやすい！
[[Typescripttypeof] "object value" [keyof typeof "object value"] の動作を丁寧に解説してみる](https://qiita.com/saba_can00/items/bdefb28a1873658cf5d9)


HttpStatus型に存在しない値は代入することができなくなりました！

```ts
// BAD 型 '1' を型 'HttpStatus' に割り当てることはできません。ts(2322)
const hoge: HttpStatus = 1

// OK
const hoge: HttpStatus = HTTP_STATUS_CODES.BAD_REQUEST
```
