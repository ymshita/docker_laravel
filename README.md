## 環境構築

- `.env.example` から .env ファイルを作成し設定

```
cp ${BACKEND}/.env.example ${BACKEND}/.env
```

- .env ファイルの以下の環境変数を設定

```
DB_DATABASE
DB_USERNAME
DB_PASSWORD
DB_ROOT_PASSWORD
POSTGRES_DB="${DB_DATABASE}"
POSTGRES_USER="${DB_USERNAME}"
POSTGRES_PASSWORD="${DB_PASSWORD}"
POSTGRES_ROOT_PASSWORD="${DB_ROOT_PASSWORD}"
MYSQL_DATABASE="${DB_DATABASE}"
MYSQL_USER="${DB_USERNAME}"
MYSQL_PASSWORD="${DB_PASSWORD}"
MYSQL_ROOT_PASSWORD="${DB_ROOT_PASSWORD}"
MYSQL_ALLOW_EMPTY_PASSWORD="${DB_PASSWORD}"
MYSQL_RANDOM_ROOT_PASSWORD="${DB_ROOT_PASSWORD}"
```

- プロジェクトルートで以下を行い `.devcontainer` 作成

```
git clone git@github.com:ymshita/.devcontainer.git
```

OR

```
mkdir .devcontainer &&\
touch .devcontainer/docker-php-ext-xdebug.ini
```

- `default.conf.template` から .env ファイルを作成し設定

```
cp ${BACKEND}/default.conf.template ${BACKEND}/default.conf
```

- コンテナを実行(`--build`指定は初回のみ)

```
docker-compose up -d --build
```

- composer パッケージをインストール

```
docker-compose exec app composer install
```

- npm パッケージをインストール

```
docker-compose exec app npm install
```

- APP_KEY を作成

```
docker-compose exec app php artisan key:generate
```

- .env ファイルを確認し、`APP_KEY=`が自動で設定されていない場合には以下のコマンドの内容を設定

```
php artisan key:generate --show
```

- DB 作成とマイグレーション

```
docker-compose exec app php artisan cache:clear && \
docker-compose exec app php artisan config:cache &&\
docker-compose exec app php artisan migrate:fresh --seed
```

- フロントエンドライブラリのコンパイル

```
docker-compose exec app npm run dev
```

- ベーシック認証キーを設定

```
sudo htpasswd -c public/.htpasswd ${BASIC_AUTH_USERNAME}
```

- ブラウザで動作確認

- [localhost](http://localhost)

## コマンド

### make 系

```
docker-compose exec app php artisan make:model Keyword -f -r -m
docker-compose exec app php artisan make:controller ArticleController
docker-compose exec app php artisan make:request ArticleRequest
```

### マイグレーション 系

```
docker-compose exec app php artisan make:migration create_XXXs_table --create=XXXs
docker-compose exec app php artisan make:migration add_column_to_users_table
docker-compose exec app php artisan migrate
docker-compose exec app php artisan migrate:rollback
docker-compose exec app php artisan migrate:rollback --step=2
docker-compose exec app php artisan migrate:status
docker-compose exec app php artisan db:seed
```

### ルートリスト

```
docker-compose exec app php artisan route:list
```

### キャッシュクリア系

```
docker-compose exec app php artisan cache:clear
docker-compose exec app php artisan config:clear
docker-compose exec app php artisan route:clear
docker-compose exec app php artisan view:clear
docker-compose build --no-cache
```

### vendor publish

```
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### Composer のメモリエラー時

```
COMPOSER_MEMORY_LIMIT=-1 $(which composer) update
```

### docker

```
docker exec -it naus_app bash
docker container prune
docker ps -aq | xargs docker rm -f
docker image prune
docker images -aq | xargs docker rmi -f
docker volume prune
docker system prune
```

### docker-compose

```
docker-compose up -d
docker-compose config
docker-compose build --no-cache
```

### next

```
docker-compose exec app npm --prefix client run build
```

### 静的解析
- 実行
    - `docker-compose exec app ./vendor/bin/psalm`
- キャッシュクリア
    - `docker-compose exec app ./vendor/bin/psalm --clear-cache`