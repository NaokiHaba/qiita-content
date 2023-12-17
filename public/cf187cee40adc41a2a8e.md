---
title: ERESOLVE unable to resolve dependency treeでパッケージがインストールできない場合の対応方法
tags:
  - npm
  - NestJS
private: false
updated_at: '2022-03-16T03:24:38+09:00'
id: cf187cee40adc41a2a8e
organization_url_name: boomtechcafe
slide: false
ignorePublish: false
---
# 概要

「依存関係がおかしいからインストールできねぇよ」って怒られた場合の対応方法をまとめる
なお、パッケージ管理ツールは`npm`を利用しています

- 解決に至った参考資料

https://stackoverflow.com/questions/64936044/fix-the-upstream-dependency-conflict-installing-npm-packages



```bash
npm i --save @nestjs/websockets @nestjs/platform-socket.io
```

https://docs.nestjs.com/websockets/gateways

```bash
npm notice 
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR! 
npm ERR! While resolving: websocket-server@0.0.1
npm ERR! Found: @nestjs/common@7.6.18
npm ERR! node_modules/@nestjs/common
npm ERR!   @nestjs/common@"^7.6.15" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! peer @nestjs/common@"^8.0.0" from @nestjs/websockets@8.4.0
npm ERR! node_modules/@nestjs/websockets
npm ERR!   @nestjs/websockets@"*" from the root project
npm ERR! 
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force, or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
```

# 解決方法

`--legacy-peer-deps`オプションを追加してインストールします

```bash
npm install --legacy-peer-deps @nestjs/websockets @nestjs/platform-socket.io
```

# 原因

`npm v7`にバージョンアップした際に`peerDependencies`をデフォルトでインストールするようになったことで、バージョンの衝突によるエラーが発生するようになったみたい

今回追加した`--legacy-peer-deps`オプションは`peerDependency`の自動インストールを回避する方法として 導入されたもので、`peer deps`を無視してインストールさせます

なお、今回参考にしたサイトでは`--save`オプションも指定してますが、`npm v5`以降はデフォルトで`save`してくれるので割愛してます

https://qiita.com/havveFn/items/c5beda8572aa8c1e6be6

https://stackoverflow.com/questions/66239691/what-does-npm-install-legacy-peer-deps-do-exactly-when-is-it-recommended-wh

> One way of thinking of this flag is that it isn't doing something new; rather it's telling NPM not to do something new, since NPM v7 now installs peerDependencies by default.
In many cases, this is leading to version conflicts, which will break the installation process.
The --legacy-peer-deps flag was introduced with v7 as a way to bypass peerDependency auto-installation; it tells NPM to ignore peer deps and proceed with the installation anyway. This is how things used to be with NPM v4 thru v6.
