# 環境構築

- `.env.example` から .env ファイルを作成し設定

```bash
APP=`cat .env | grep APP= | sed s/APP=//` &&\
cp ../${APP}/.env.example ../${APP}/.env
```

- .env ファイルの以下の環境変数を設定

```.env
# DB_DATABASE=
# DB_USERNAME=
# DB_PASSWORD=
# DB_ROOT_PASSWORD=
```

- プロジェクトルートで以下を行い `.devcontainer` 作成

```bash
git clone git@github.com:ymshita/.devcontainer.git
```

OR

```bash
mkdir .devcontainer &&\
touch .devcontainer/docker-php-ext-xdebug.ini
```

- `default.conf.template` から .env ファイルを作成し設定

```bash
APP_DIR=`cat .env | grep APP_DIR | sed s/APP_DIR=//`
cp infra/nginx/default.conf.template infra/nginx/default.conf
```

- コンテナを実行(`--build`指定は初回のみ)

```bash
docker-compose up -d --build
```

- composer パッケージをインストール

```bash
docker-compose exec app composer install
```

- npm パッケージをインストール

```bash
docker-compose exec app npm install
```

- APP_KEY を作成

```bash
docker-compose exec app php artisan key:generate
```

- .env ファイルを確認し、`APP_KEY=`が自動で設定されていない場合には以下のコマンドの内容を設定

```bash
php artisan key:generate --show
```

- DB 作成とマイグレーション

```bash
docker-compose exec app php artisan cache:clear && \
docker-compose exec app php artisan config:cache && \
docker-compose exec app php artisan migrate:fresh --seed
```

- ストレージの公開領域用のシンボリックリンクを作成（必要に応じ）

```bash
docker-compose exec app php artisan storage:link
```

- ログの書き込み権限変更

```bash
docker-compose exec app chmod -R a+w ./storage
```

- フロントエンドライブラリのコンパイル

```bash
docker-compose exec app npm run dev
```

- ベーシック認証キーを設定

```bash
sudo htpasswd -c public/.htpasswd ${BASIC_AUTH_USERNAME}
```

- ブラウザで動作確認

- [localhost](http://localhost)

## コマンド

### make 系

```bash
docker-compose exec app php artisan make:model Keyword -f -r -m
docker-compose exec app php artisan make:controller ArticleController
docker-compose exec app php artisan make:request ArticleRequest
```

### マイグレーション 系

```bash
docker-compose exec app php artisan make:migration create_XXXs_table --create=XXXs
docker-compose exec app php artisan make:migration add_column_to_users_table
docker-compose exec app php artisan migrate
docker-compose exec app php artisan migrate:rollback
docker-compose exec app php artisan migrate:rollback --step=2
docker-compose exec app php artisan migrate:status
docker-compose exec app php artisan db:seed
```

### ルートリスト

```bash
docker-compose exec app php artisan route:list
```

### キャッシュクリア系

```bash
docker-compose exec app php artisan cache:clear
docker-compose exec app php artisan config:clear
docker-compose exec app php artisan route:clear
docker-compose exec app php artisan view:clear
docker-compose build --no-cache
```

### vendor publish

```bash
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### Composer のメモリエラー時

```bash
COMPOSER_MEMORY_LIMIT=-1 $(which composer) update
```

### docker

```bash
docker exec -it naus_app bash
docker container prune
docker ps -aq | xargs docker rm -f
docker image prune
docker images -aq | xargs docker rmi -f
docker volume prune
docker system prune
```

### docker-compose

```bash
docker-compose up -d
docker-compose config
docker-compose build --no-cache
```

### next

```bash
docker-compose exec app npm --prefix client run build
```

### 静的解析

- 実行
  - `docker-compose exec app ./vendor/bin/psalm`
- キャッシュクリア
  - `docker-compose exec app ./vendor/bin/psalm --clear-cache`
