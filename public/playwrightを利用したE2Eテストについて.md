---
title: playwrightを利用したE2Eテストについて
tags:
  - テスト
  - TypeScript
  - e2e
  - Playwright
private: true
updated_at: '2023-12-17T23:10:27+09:00'
id: b4853861419fbc23816d
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

この記事は [テスト自動化の成功事例を語ろう by T-DASH Advent Calendar 2023](https://qiita.com/advent-calendar/2023/t-dash02) の18日目の記事です。

https://qiita.com/advent-calendar/2023/t-dash02

この記事では、ハンズオン形式でplaywrightを利用したE2Eテストを実装する方法について紹介します。

実装したソースコードは以下のリポジトリにありますので、ご興味ある方はご覧ください。

https://github.com/NaokiHaba/playwright-next-app-sample

## 想定する読者

この記事は以下のような方を想定しています。

- playwrightを触ってみたい方
- E2Eテストを実装したい方

## ハンズオンを行うにあたって事前に準備しておくもの

このハンズオンを行うにあたって以下のものを事前に準備しておいてください。

- Node.js のインストール
  - https://nodejs.org/en/download/package-manager/
- GitHubアカウントの作成
  - https://github.com/
- GitHubリポジトリの作成
  - https://docs.github.com/ja/repositories/creating-and-managing-repositories/quickstart-for-repositories

## この記事のゴール

この記事を読み終えると `playwright` を利用したE2Eテストを実装する方法がわかるようになることを目指します。

## 実行環境

この記事を書いた時の実行環境は以下の通りです。

今回は一例として以下の環境で実行していますが、お好きな環境で実行していただいて問題ありません。

- Next.js v14.0.3
- playwright v1.40.1
- Mac OS Sonoma v14.1.1
- pnpm v8.6.6
- Node.js v20.3.1

https://github.com/NaokiHaba/playwright-next-app-sample を `Clone` して `pnpm install` していただければ、この記事の実行環境を再現できます。

# playwrightを利用したE2Eテストについて

## playwrightとは

https://github.com/microsoft/playwright

- Chromium、WebKit、Firefoxを含むすべての最新のレンダリングエンジンをサポートしているNode.jsベースのライブラリ
- ブラウザの自動化、スクレイピング、テストができる
- PuppeteerとPlaywrightはほとんど同じチームによって開発されている
- Microsoftが開発したE2Eテストツール

以下のブログでPuppeteerとPlaywrightの比較が分かりやすくまとめられているので、興味ある方はご覧ください。

https://blog.logrocket.com/playwright-vs-puppeteer/

## Playwrightの特徴的な機能

### Test generator

https://playwright.dev/docs/codegen

- ブラウザでの操作を記録して、その操作を再現するコードを生成する機能
- 生成されたコードをベースにテストを実装できる
- 生成されたコードはTypeScriptで書かれている

Test generator は以下のコマンドで実行できます。

```shell
# ここでは pnpm を利用していますが、npm や yarn でも実行できます
$ pnpm exec playwright codegen demo.playwright.dev/todomvc
```

Test generator を起動すると以下のような画面が表示されます。

![スクリーンショット 2023-12-17 22.19.10.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/04450274-4a15-c1aa-ba85-2e48a817efb0.png)

この画面でブラウザでの操作を記録していくと、その操作を再現するコードが生成されます。

一例として、以下の操作を記録してみましょう。

1. `What needs to be done?` の入力欄をクリックする
2. `What needs to be done?` の入力欄に `テスト` と入力する
3. `What needs to be done?` の入力欄に `テスト` と入力しされていることを確認する

記録が完了すると以下のようなコードが生成されます。

```typescript
import { test, expect } from '@playwright/test';

test('test', async ({ page }) => {
  await page.goto('https://demo.playwright.dev/todomvc/#/');
  await page.getByPlaceholder('What needs to be done?').click();
  await page.getByPlaceholder('What needs to be done?').fill('テスト');
  await expect(page.getByPlaceholder('What needs to be done?')).toHaveValue('テスト');
});
```

### UI Mode

https://playwright.dev/docs/test-ui-mode

- Playwright v1.32.0 から追加された機能
- 専用のウィンドウが立ち上がり、テストの実行結果の確認やデバッグができる
- テストの実行結果は、テストの実行中にリアルタイムで表示される

UI Mode は以下のコマンドで実行できます。

```shell
$ pnpm exec playwright test --ui
```

UI Mode を起動すると以下のような画面が表示されます。

![スクリーンショット 2023-12-17 22.28.37.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/4a82c15b-bcef-631f-a576-e44a32303f8c.png)

この画面でテストを実行すると、テストの実行結果がリアルタイムで表示されます。

画面上部にテストが完了するまでの一連の操作がキャプチャされていきます。

テストが失敗した場合に脳内で再現するのが難しい場合には、この機能を利用するとテストの失敗した箇所を確認できるので個人的にはとても便利だと思っています。

## ハンズオン

ここからは実際にplaywrightを利用したE2Eテストを実装していきます。

途中 Next.js のプロジェクトを作成も行いますが、お好きなFWで実装していただいて問題ありません。

### Next.jsをセットアップする

Next.jsのセットアップ方法は、こちらを参考にしてください。

https://nextjs.org/docs/getting-started/installation

今回は ~/Documents に Next.jsをインストールしていますが、お好きなディレクトリにインストールしていただいて問題ありません。

```shell
# 任意のディレクトリに移動してください
$ cd ~/Documents

# プロジェクト名は任意のものを指定してください
# ここでは、playwright-next-app-sample というプロジェクト名で作成しています
$ npx create-next-app@latest

$ cd playwright-next-app-sample

# pnpm を利用していますが、npm や yarn・bunでも問題ありません。お好きなものを利用してください
$ pnpm dev
```

http://localhost:3000/ にアクセスして、以下のような画面が表示されればNext.jsのセットアップは完了です。

![スクリーンショット 2023-12-17 22.35.30.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/68bb93c6-a5f3-5128-d4f3-0d0970059ea4.png)

### playwrightをセットアップする

playwrightのセットアップ方法は、こちらを参考にしてください。

https://playwright.dev/docs/intro

```shell
$ pnpm create playwright

Choose between TypeScript or JavaScript (default is TypeScript) # 今回は TypeScript を選択しています
Name of your Tests folder (default is tests or e2e if you already have a tests folder in your project) # 任意のフォルダ名を入力 (今回は tests を入力)
Add a GitHub Actions workflow to easily run tests on CI
Install Playwright browsers (default is true) # GitHub Actions workflow を利用する場合は true を選択してください
```

playwrightのセットアップが完了すると、以下のようなフォルダ構成になります。

```shell
- tests
- tests-example
- playwright.config.ts
```

それでは、実際にテストを実行してみましょう。

```shell
$ pnpm exec playwright test

➜  playwright-next-app-sample git:(main) ✗     pnpm exec playwright test

Running 6 tests using 5 workers
  6 passed (4.1s)

To open last HTML report run:
  pnpm exec playwright show-report
```

テストが実行され、全てのテストがパスすることが確認できました。

#### テストの実行結果を確認する

テストの実行結果は、`playwright-report` ディレクトリに保存されます。

`$ pnpm exec playwright show-report` を実行すると、実行結果をブラウザで確認できます。

```shell
$ pnpm exec playwright show-report
```

![スクリーンショット 2023-12-17 22.40.14.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/c8ea4f20-da96-0210-a574-248b3907c197.png)

### テストを実装する

それでは、実際にテストを実装していきましょう。

今回は簡単なページ遷移のテストを実装していきます。

`tests` ディレクトリに既に `example.spec.ts` が存在していますので、こちらを更新していきます。

```typescript:tests/example.spec.ts
import { expect, test } from '@playwright/test';

// テストコードの実行前にTOPページにアクセスする
test.beforeEach(async ({page}) => {
    await page.goto('http://localhost:3000');
});

test('Get started by editing src/app/page.tsx が表示される', async ({page}) => {
    await expect(page.getByRole('main')).toContainText('Get started by editing src/app/page.tsx');
})

test('Docページに遷移できる', async ({page}) => {
    const page7Promise = page.waitForEvent('popup');
    await page.getByRole('link', {name: 'Docs -> Find in-depth'}).click();
    const page7 = await page7Promise;
    await expect(page7.getByRole('heading', {name: 'Introduction'})).toBeVisible();
})
```

### テストの実行

それでは、冒頭で紹介した `UI Mode` を利用してテストを実行してみましょう。

```shell
$ pnpm exec playwright test --ui
```

テストを実行すると、以下のような画面が表示されます。

![スクリーンショット 2023-12-17 22.44.15.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/b4556f3f-a0ab-0bb9-115f-ac69129302b9.png)

画面上部にテストが完了するまでの一連の操作がキャプチャされていきます。

### GitHub Actionsでテストを実行する

最後に、GitHub Actionsでテストを実行してみましょう。

Playwrightのセットアップ時に、GitHub Actionsの設定ファイルを生成したので、それを利用してテストを実行します。

```ymal:.github/workflows/playwright.yml
name: Playwright Tests
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install dependencies
        run: npm install -g pnpm && pnpm install
      - name: Install Playwright Browsers
        run: pnpm exec playwright install --with-deps
      - name: Run Playwright tests
        run: pnpm exec playwright test
      - uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

#### playwrightの設定ファイルを変更する

GitHub Actionsでテストを実行するにあたって、playwrightの設定ファイルを変更する必要があります。

今回は、ローカル環境でテストを実行しますので、テスト実行前にローカルサーバーを起動する必要があります。

```typescript:playwright.config.ts
export default defineConfig({
    /* Run your local dev server before starting the tests */
    webServer: {
        command: 'pnpm run dev',
        url: 'http://127.0.0.1:3000',
        reuseExistingServer: !process.env.CI
    }
});
```

#### テストの結果を確認する

それでは、ここまでの差分をコミットして、GitHubにプッシュしてください。

GitHub Actionsでテストが実行されていることを確認することができます。

![スクリーンショット 2023-12-17 22.51.32.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/555632/815c2ea4-bff4-bb24-74d0-5ee6faa8e63c.png)

テストの実行結果は、GitHub Actionsの `Artifacts` から確認できます。

# 最後に

Playwrightは、Puppeteerと比較しても遜色ない機能を持っているので、今後はPlaywrightを利用したテストを実装していきたいと思っています。

この記事を読んで、Playwrightを利用したテストを実装してみたいと思っていただけたら幸いです。
