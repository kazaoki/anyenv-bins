FROM centos:7

ENV INSTALL_PHP_VERSION="7.3.6"

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

# -- cmakeのインストール　https://qiita.com/m3m0r7/items/95315592f9c06e88dd40
RUN yum install -y wget
RUN wget https://cmake.org/files/v3.13/cmake-3.13.3-Linux-x86_64.tar.gz -O cmake-3.13.3-Linux-x86_64.tar.gz && \
    tar zxvf cmake-3.13.3-Linux-x86_64.tar.gz
RUN rm -rf /usr/share/aclocal && \
    rm -rf /usr/share/applications && \
    rm -rf /usr/share/mime && \
    rm -rf /usr/share/icons
RUN mv -f cmake-3.13.3-Linux-x86_64/bin/* /usr/bin/ && \
    mv -f cmake-3.13.3-Linux-x86_64/share/* /usr/share/

# -- libzipのインストール　https://qiita.com/m3m0r7/items/95315592f9c06e88dd40
RUN mkdir /tmp/libzip && \
    cd /tmp/libzip && \
    curl -sSLO https://libzip.org/download/libzip-1.5.1.tar.gz && \
    tar zxf libzip-1.5.1.tar.gz && \
    cd libzip-1.5.1/ && \
    mkdir build && \
    cd build && \
    cmake ../ && \
    make && \
    make install

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv
RUN echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc
RUN /root/.anyenv/bin/anyenv install --force-init
RUN echo 'eval "$(anyenv init -)"' >> ~/.bashrc
RUN . ~/.bashrc && anyenv install phpenv
RUN . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --with-pdo-pgsql --with-pgsql --with-freetype-dir=/usr/include/freetype2/freetype --with-imap=/usr/lib64/ --with-imap-ssl" phpenv install -v $INSTALL_PHP_VERSION
RUN . ~/.bashrc && phpenv rehash

# -- copy modules & misc
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN \cp -f /etc/httpd/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules
RUN \cp -f /usr/local/lib64/libzip.so* /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules
RUN echo 'sendmail_path = /usr/sbin/sendmail -t -i' >> /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/etc/php.ini

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
