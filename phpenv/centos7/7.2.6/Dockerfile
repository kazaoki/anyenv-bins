FROM centos:7

ENV INSTALL_PHP_VERSION="7.2.6"

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
        postgresql-devel \
        freetype-devel \
        libc-client-devel

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv
RUN echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc
RUN echo 'eval "$(anyenv init -)"' >> ~/.bashrc
RUN . ~/.bashrc && anyenv install phpenv
RUN . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --with-pdo-pgsql --with-pgsql --with-freetype-dir=/usr/include/freetype2/freetype --with-imap=/usr/lib64/ --with-imap-ssl" phpenv install -v $INSTALL_PHP_VERSION

# -- copy httpd module
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN \cp -f /etc/httpd/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
