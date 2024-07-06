FROM ubuntu:24.04

# docker build -t kazaoki/ubuntu-phpenv:7.4.28 .

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

ENV INSTALL_PHP_VERSION="7.4.28"

# -- packages setup
RUN apt update && \
    apt install -y \
        git \
        curl \
        bzip2 \
        build-essential \
        pkg-config \
        apache2 apache2-dev \
        libxml2 libxml2-dev \
        libkrb5-dev \
        libssl-dev \
        libsqlite3-dev \
        zlib1g-dev \
        libbz2-dev \
        libcurl4-openssl-dev \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libc-client-dev \
        libonig-dev \
        libpq-dev \
        libreadline-dev \
        libtidy-dev \
        libxslt-dev \
        libzip-dev \
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv \
    && echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc \
    && /root/.anyenv/bin/anyenv install --force-init \
    && echo 'eval "$(anyenv init -)"' >> ~/.bashrc \
    && . ~/.bashrc && anyenv install phpenv \
    && . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --with-pdo-pgsql --with-pgsql --with-freetype=/usr/include/freetype2/freetype --with-imap=/usr/lib --with-libdir=lib --with-imap-ssl --with-kerberos --enable-opcache" phpenv install -v $INSTALL_PHP_VERSION \
    && . ~/.bashrc && phpenv rehash

# -- copy modules & misc
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules \
    && \cp -f /usr/lib/apache2/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules \
    && mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && \cp -f /usr/lib/x86_64-linux-gnu/libzip.so* /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && \cp -f /usr/lib/x86_64-linux-gnu/libonig.so* /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && echo 'sendmail_path = /usr/sbin/sendmail -t -i' >> /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/etc/php.ini

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
