# Webベース環境
FROM nginx:mainline AS web-base

# ポートの開放
EXPOSE 80

# OpenSSLのインストール
RUN apt-get update && apt-get install -y openssl



# Web開発環境
FROM web-base AS web-dev

# confのコピー
COPY ./nginx/conf_dev/ /etc/nginx/conf/



# Web本番環境
FROM web-base AS web-prod

# confのコピー
COPY ./nginx/conf_prod/ /etc/nginx/conf/



#PHPベース環境
FROM php:8.3.2-fpm AS app-base

# Vite用のポート開放
EXPOSE 5173

# 必須パッケージのインストール
RUN apt-get update && apt-get install -y git libzip-dev zip

# PHP拡張モジュールのインストール
RUN docker-php-ext-install zip pdo_mysql

# Composerのインストール
COPY --from=composer:lts /usr/bin/composer /usr/bin/composer

# Node.jsのインストール
COPY --from=node:lts  /usr/local/bin/node /usr/local/bin/node
COPY --from=node:lts  /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node:lts  /usr/local/bin/npm /usr/local/bin/npm

# npm用のシンボリックリンクを作成
RUN ln -s /usr/local/lib/node_modules/npm/lib/cli.js /usr/local/lib/cli.js

# npmを最新版にアップデート
RUN npm install -g npm



# PHP開発環境
FROM app-base AS app-dev

# Composer用の環境変数を設定
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_PROCESS_TIMEOUT 3600

# Laravelのインストール
RUN composer global require "laravel/laravel"

