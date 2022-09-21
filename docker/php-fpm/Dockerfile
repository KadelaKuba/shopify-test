FROM php:8.1-fpm as base

ARG project_root=.
ARG user_gid=1000
ARG user_uid=1000

# Setup non-root user to avoid permission issues later
RUN addgroup --gid ${user_gid} trayto && \
    adduser --disabled-password --uid ${user_uid} --ingroup trayto trayto && \
    mkdir -p /var/www/html && \
    chown -R trayto:trayto /var/www/html

COPY --from=composer /usr/bin/composer /usr/bin/composer

# install required tools
# git for computing diffs
# wget for installation of other tools
# libzip-dev needed by "zip" extension for PHP
# gnupg and g++ for gd extension
# locales for locale-gen command
# apt-utils so package configuartion does not get delayed
# unzip to ommit composer zip packages corruption
# dialog for apt-get to be
# git for computing diffs and for npm to download packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    wget gnupg g++ locales libzip-dev zip unzip dialog apt-utils git \
    vim nano mc htop bash-completion \
    && apt-get clean

RUN docker-php-ext-install zip && \
    docker-php-ext-enable zip opcache

RUN apt-get -y update \
&& apt-get install -y libicu-dev \
&& docker-php-ext-configure intl \
&& docker-php-ext-install intl \
&& apt-get clean

# Install NodeJS
RUN curl -fsSL https://deb.nodesource.com/setup_16.x | bash -
RUN apt-get update && apt-get install -y nodejs && apt-get clean

# enable bash completion
RUN echo "source /etc/bash_completion" >> ~/.bashrc

# Switch to non-root user. Rest of dockerfile and at runtime is under this user.
USER trayto

WORKDIR /var/www/html
