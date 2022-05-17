FROM php:7.4-fpm-buster
SHELL ["/bin/bash", "-oeux", "pipefail", "-c"]

# set envs
ENV COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer
ARG TZ=Asia/Tokyo
ENV TZ ${TZ}
ENV LANG C.UTF-8
ENV WORKSPACE=/usr/local/src
COPY ./${APP_DIR} $WORKSPACE

COPY --from=composer:2.1 /usr/bin/composer /usr/bin/composer

# install bundler.
RUN apt-get update && \
  apt-get install -y sudo && \
  apt-get install -y vim less && \
  apt-get -y install git unzip libzip-dev libicu-dev libonig-dev && \
  apt-get install -y libpq-dev && \
  apt-get install -y apache2-utils && \
   apt-get install -y libc-client-dev libkrb5-dev &&\
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* && \
  docker-php-ext-install intl pdo_mysql zip bcmath && \
  docker-php-ext-install pdo pdo_pgsql pgsql mbstring &&\
  docker-php-ext-configure imap --with-kerberos --with-imap-ssl &&\
  docker-php-ext-install imap &&\
  pecl install xdebug && docker-php-ext-enable xdebug

# install Node.js and npm.
RUN apt-get update && apt-get install -y curl apt-transport-https wget
# RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
#     echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
#     apt-get update && apt-get install -y yarn
RUN curl -sL https://deb.nodesource.com/setup_current.x | bash -
RUN apt-get install -y nodejs
RUN npm install npm@latest -g

# create user and group.
# RUN groupadd -r --gid 1000 dev && \
#   useradd -m -r --uid 1000 --gid 1000 dev

# create directory.
# RUN mkdir -p $WORKSPACE&& \
#   chown -R dev:dev $WORKSPACE

COPY ./infra/php/php.ini /usr/local/etc/php/php.ini

COPY ./.devcontainer/docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# USER dev
WORKDIR $WORKSPACE

# ADD ./${APP_DIR}/composer.json /work/composer.json
# ADD ./${APP_DIR}/composer.lock /work/composer.lock
# RUN composer global require "laravel/installer"

# CMD COMPOSER_MEMORY_LIMIT=-1 composer require "laravel/jetstream" && \
#   /usr/local/src/artisan jetstream:install livewire  && \
#   npm install && \
#   npm run dev