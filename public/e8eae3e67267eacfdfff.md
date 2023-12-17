---
title: 【Laravel8.x】observerでEloquentモデルのイベントをディスパッチする
tags:
  - Laravel
  - Eloquent
  - observer
private: false
updated_at: '2022-03-01T20:43:53+09:00'
id: e8eae3e67267eacfdfff
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

`Eloquent`に用意されている`observer`を利用して`save`メソッドを検知して処理を追加する方法を備忘録として紹介します

https://readouble.com/laravel/8.x/ja/eloquent.html


# 解消する問題

1.ログインユーザー名を取得する
2.1と`モデルインスタンス`に属性の配列を加えた配列を結合
3.`save()`メソッドで登録する

1.2の処理を更新時にも同じ処理を記述するのはイケてないので`observer`にやってもらいます

```php
$loginUser = Auth::user()->name;

$storeData = array_merge(
    (new User())->fill($request->all())->toArray(),
    ['create_user' => $loginUser],
    ['update_user' => $loginUser],
);

$result = (new User())->fill($storeData)->save();
```

# やり方

## `observer`を生成

```bash
php artisan make:observer UserObserver --model=User
```

## `observer`に処理を定義

- 今回は`save()`が成功した後に登録者・更新者を登録したいので`saved()`メソッドを利用

`saved(User $user)`:`$user`には`save()`メソッドの結果(登録したユーザーのモデルインスタンス情報）が入ります

```php:src/laravel/app/Observers/UserObserver.php
<?php

namespace App\Observers;

class UserObserver
{
    /**
     * Handle the user "save" event.
     *
     * @param User $user
     * @return void
     */
    public function saved(User $user): void
    {
        $loginUser = Auth::user()->name;
        $saveData = [
            'create_user' => $loginUser,
            'update_user' => $loginUser
        ];

        User::query()->where('id', $user->id)->update($saveData);
    }
}
```

## `observer`を`AppServiceProvider`で読み込む

```php:src/laravel/app/Providers/AppServiceProvider.php
class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot(): void
    {
        User::observe(UserObserver::class);
    }
}
```

## `observer`に移した処理を削る

スッキリかけました

なお、今回`observer`でクエリビルダで`update`していますが`save()`メソッドを利用したい場合は`saveQuietly()`メソッドを利用してください

:::note alert
save()メソッドをobserverで実行するとイベントをディスパッチしてしまうので処理が終わりません
必ずsaveQuietly()メソッドを利用するようにしてください
:::

https://readouble.com/laravel/8.x/ja/eloquent.html

```php
$result = (new User())->fill($request->all())->save();
```

# おわりに

今回は簡単な例で`observer`について紹介しました
登録前の処理は`saving`・削除前は`deleting`等、イベントを検知することでビジネスロジックがスッキリ書ける場合もあるので使ってみるといいかもしれません。
この記事が参考になれば幸いです
