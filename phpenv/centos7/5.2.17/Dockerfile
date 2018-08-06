FROM centos:7

ENV INSTALL_PHP_VERSION="5.2.17"

# -- packages setup
RUN yum install -y epel-release
RUN yum update -y
RUN yum install -y \
        git \
        bzip2 \
        bzip2-devel \
        g++ gcc \
        libevent-devel \
        libxml2-devel \
        openssl-devel \
        libcurl-devel \
        libjpeg-devel \
        libpng-devel \
        re2c \
        libicu-devel \
        gcc-c++ \
        libmcrypt-devel \
        readline-devel \
        libtidy-devel \
        libxslt-devel \
        file \
        make \
        autoconf \
        patch \
        bison \
        lemon \
        libsql \
        httpd-devel \
        mariadb-devel \
        postgresql-devel

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv
RUN echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc
RUN echo 'eval "$(anyenv init -)"' >> ~/.bashrc
RUN . ~/.bashrc && anyenv install phpenv

# -- direct source download for 5.2.17
RUN mkdir -p /tmp/php-build/packages
RUN cd /tmp/php-build/packages && curl -OL http://museum.php.net/php5/php-5.2.17.tar.gz
RUN mkdir -p /tmp/php-build/source
RUN cd /tmp/php-build/source && tar zxfv /tmp/php-build/packages/php-5.2.17.tar.gz -C ./ && mv php-5.2.17 5.2.17

# -- source modify
RUN sed -i 's/AP_DECLARE_DATA extern unixd_config_rec unixd_config;/AP_DECLARE_DATA extern unixd_config_rec ap_unixd_config;/' /tmp/php-build/source/5.2.17/sapi/apache2handler/php_functions.c
RUN sed -i 's/unixd_config.user_name, unixd_config.user_id, unixd_config.group_id);/ap_unixd_config.user_name, ap_unixd_config.user_id, ap_unixd_config.group_id);/' /tmp/php-build/source/5.2.17/sapi/apache2handler/php_functions.c

# -- recompile
RUN . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --with-pdo-pgsql --with-pgsql" phpenv install -v $INSTALL_PHP_VERSION

# -- copy httpd module
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN \cp -f /etc/httpd/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
