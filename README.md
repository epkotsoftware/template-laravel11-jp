# Laravel11環境

Laravel11 のGitHubテンプレートリポジトリです。

## インストール

- Git (GitHub)
- Docker Desktop
- ターミナル (Windowsの場合、「PowerShell」, 「GitBash」等)
- VSCode (任意)
- SQLクライアント (任意 「A5:SQL Mk-2」、「DBeaver」等)

## テンプレート導入

導入は、ダウンロードするか、テンプレートからリポジトリを作成してください。

### ダウンロードする場合

「Code」から「Download ZIP」を選択するとダウンロードが可能です。

### リポジトリを作成する場合

#### リポジトリ作成

「Use this template」ボタンで、テンプレートから自身のリポジトリを作成してください。

- テンプレートからリポジトリを作成する
  - <https://docs.github.com/ja/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template>

#### clone

自身で作成したリポジトリをcloneしてください。

- リポジトリをクローンする
  - <https://docs.github.com/ja/repositories/creating-and-managing-repositories/cloning-a-repository>

## 開発環境

Dockerを使って環境を構築します。  

### 構成図

![Docker](./docs/images/docker.svg)

### 構築する環境

- Webコンテナ
  - [php:8.2.16-apache](https://hub.docker.com/_/php)
  - [composer:2.7.1](https://hub.docker.com/_/composer)
- DBコンテナ
  - [mysql:8.0.36](https://hub.docker.com/_/mysql)
- phpMyAdminコンテナ
  - [phpmyadmin:5.2.1](https://hub.docker.com/_/phpmyadmin)

### .env

[.env](./.env)ファイルはDockerの環境ファイルです。  
各名称・ポート設定をしてください。  
基本的にはそのまま使用可能ですが、IPとポートが重複するとコンテナが起動しないので  
自身の環境に合わせて設定を変えてください。

### compose

以下のコマンドを実行します。

```bash
# ターミナルで実行
## ls コマンドで docker-compose.yml があるか確認
ls docker-compose.yml
## docker-compose で環境構築  ※ 時間がかかるので注意
docker-compose up -d
```

上記コマンドでエラーがなければ環境構築が完了しています。

### Laravel

Laravel関連のコマンドはDockerで用意した、Webサーバー（コンテナ）上で行います。

```bash
# ターミナルで実行
## Webサーバーに入るコマンド（-itの後に入る名称はコンテナ名「{NAME_PREFIX}-web」）
docker exec -it laravel11-web bash
```

VSCodeの[Docker拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)が入っている場合、対象コンテナの「Attach Shell」でも開けます。  

#### composer install

```bash
# ■ Webサーバーで入力
# 「composer.json」、「composer.lock」に記載されているパッケージをvendorディレクトリにインストール
#   ※ 時間がかかるので注意。
composer install
```

`composer install` 実行後に「`Exception`」・「`Error`」等が出ていると失敗しているので  
[root/vendor/](./root/vendor/)ディレクトリを削除して、再実行してみましょう。  

#### Laravel初期設定

```bash
# ■ Webサーバーで入力
cd /var/www/root
# 「.env」ファイル
## 「.env.dev」ファイルを「.env」にコピー
cp .env.dev .env
# storage ディレクトリに読み取り・書き込み権限を与える（bootstrap, storage内に書き込み（ログ出力時等）に「Permission denied」のエラーが発生する）
chmod -R 777 bootstrap/cache/
chmod -R 777 storage/
```

### 確認

- Web ※ **IP・ポート番号は [`.env`](./.env) の `IP`・`PORT_WEB` を参照**
  - <http://127.0.0.1:80/> （デフォルト設定のURL）  
    [routes/web.php](./root/routes/web.php)のURI「`'/'`」の実行結果が画面に表示されます。  
    VSCodeの[Docker拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)が入っている場合、対象コンテナの「Open in Browser」でも開けます。  
- phpMyAdmin ※ **IP・ポート番号は [`.env`](./.env) の `IP`・`PORT_PHPMYADMIN` を参照**
  - <http://127.0.0.1:8080> （デフォルト設定のURL）  
    VSCodeの[Docker拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)が入っている場合、対象コンテナの「Open in Browser」でも開けます。  

### SQLクライアント

こちらは任意です。

- `DBeaver`
  - <https://dbeaver.io/>
  - 接続情報 ※ [`.env`](./.env) の情報にあわせて設定すること
    - ドライバ名: `MySQL`
    - ServerHost: `localhost`  ～  `IP` 参照 (localhost = 127.0.0.1)
    - Port: `3306`  ～  `PORT_DB` 参照
    - Database: ※ 未入力でOK
    - ユーザー名: `root`
    - パスワード: `root`  ～  `DB_ROOT_PASSWORD` 参照
- `A5:SQL Mk-2`
  - <https://a5m2.mmatsubara.com/>
  - 接続情報 ※ [`.env`](./.env) の情報にあわせて設定すること
    - ホスト名: `localhost`  ～  `IP` 参照 (localhost = 127.0.0.1)
    - ユーザーID: `root`
    - パスワード: `root`  ～  `DB_ROOT_PASSWORD` 参照
    - ポート番号: `3306`  ～  `PORT_DB` 参照

### Laravel設定

#### 言語フォルダスカフォールド

※ **以下は実施済みです**  

Laravel10から、デフォルトでlangディレクトリが追加されなくなったため  
カスタマイズできるようにlangディレクトリをスカフォールドします。

```bash
php artisan lang:publish
```

- 参考
  - `Laravel 10.x 多言語化 〜 言語ファイルのリソース公開`
    - <https://readouble.com/laravel/10.x/ja/localization.html#publishing-the-language-files>

#### 日本語言語ファイル

※ **以下は実施済みです**  

2024/03 現在、今までダウンロードできたLaravel8の日本語言語ファイルがダウンロードできなくなりました。  
以前、ダウンロードした `lang/ja` をそのまま残しています。  

#### app.php

※ **以下は実施済みです**  

`config/app.php` の日本設定を行います。  
Laravel11からは、`.env`ファイルで設定を行います。

| .env | Key | Value | 備考 |
| --- | --- | :---: | --- |
| APP_TIMEZONE        | timezone        | `Asia/Tokyo` | デフォルト: `UTC` |
| APP_LOCALE          | locale          | `ja`         | デフォルト: `en` |
| APP_FALLBACK_LOCALE | fallback_locale | `en`         | デフォルト: `en`<br>locale の言語が見つからない場合に適用する言語<br>デフォルトの`en`を指定するのが良い |
| APP_FAKER_LOCALE    | faker_locale    | `ja_JP`      | デフォルト: `en_US` |

#### .env.testing設定

※ **以下は実施済みです（migrationは必要）**  

Laravel UnitTest用の設定を行います。  
[.env.testing](./root/.env.testing)ファイルを作成し、DB_*にテスト用データベース（laravel_test）を設定します。  
UnitTest実行時に.envファイルの代わりに使われるためAPP_KEYなどの設定も必要です。  

テスト用データベースにmigrationを適用には、以下のコマンドを実行します。  

```sh
php artisan migrate --env=testing
```

- `Laravel 10.x テスト: テストの準備`
  - <https://readouble.com/laravel/10.x/ja/testing.html>

#### Laravel Debugbar

※ **以下は実施済みです**  

Laravelで作成した画面の下にデバッグバーが表示され、様々な情報が見れるようになります。

- GitHub
  - <https://github.com/barryvdh/laravel-debugbar>
  - Release
    - <https://github.com/barryvdh/laravel-debugbar/releases>

```bash
# ■ Webサーバーで入力
cd /var/www/root
# composer.json にパッケージを追加し、インストールする。 「--dev」をつけることで開発環境のみに適用。
composer require --dev barryvdh/laravel-debugbar
# config/debugbar.php を追加。
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```
