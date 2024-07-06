FROM almalinux:9.4

ENV INSTALL_PHP_VERSION="8.1.0"

# -- packages setup
RUN yum install -y epel-release
RUN yum update -y
RUN yum groupinstall -y "Development Tools"
# RUN yum remove libcurl-minimal --skip-broken --nobest && yum install libcurl libcurl-devel
RUN yum install -y \
        git \
        httpd httpd-devel \
        libxml2-devel \
        krb5-libs krb5-devel \
        openssl-devel \
        sqlite sqlite-devel \
        bzip2 bzip2-devel \
        # libcurl libcurl-devel \
        libcurl-devel \
        libpng libpng-devel \
        libjpeg-turbo libjpeg-turbo-devel \
        freetype freetype-devel \
        libpng libpng-devel \
        libjpeg-turbo libjpeg-turbo-devel \
        wget \
        libstdc++-devel \
        glibc glibc-devel \
        zlib zlib-devel \
        bzip2 bzip2-devel \
        libmcrypt libmcrypt-devel \
        gmp gmp-devel \
        libicu libicu-devel \
        re2c \





cd /usr/local/src
wget https://github.com/kkos/oniguruma/archive/refs/tags/v6.9.7.tar.gz
tar -xzf v6.9.7.tar.gz
cd oniguruma-6.9.7
./autogen.sh
./configure
make
make install
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH



export CFLAGS="-I/usr/include -I/usr/include/openssl"
export LDFLAGS="-L/usr/lib64"
export LD_LIBRARY_PATH="/usr/lib64:$LD_LIBRARY_PATH"



        # bzip2 \
        # bzip2-devel \
        # g++ gcc \
        # libevent-devel \
        # libcurl-devel \
        # libjpeg-devel \
        # libpng-devel \
        # re2c \
        # libicu-devel \
        # gcc-c++ \
        # libmcrypt-devel \
        # readline-devel \
        # libtidy-devel \
        # libxslt-devel \
        # file \
        # make \
        # wget \
        # autoconf \
        # patch \
        # bison \
        # lemon \
        # libsql \
        # mariadb-devel \
        # postgresql-devel \
        # freetype-devel \
        # libc-client-devel \
        # oniguruma oniguruma-devel \
        && rm -rf /var/cache/yum/* \
        && yum clean all

# # -- for libzip 0.11
# RUN yum install -y http://packages.psychotic.ninja/7/base/x86_64/RPMS/psychotic-release-1.0.0-1.el7.psychotic.noarch.rpm \
#     && yum --enablerepo=psychotic-plus install -y libzip libzip-devel \
#     && rm -rf /var/cache/yum/* \
#     && yum clean all

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv \
    && echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc \
    && /root/.anyenv/bin/anyenv install --force-init \
    && echo 'eval "$(anyenv init -)"' >> ~/.bashrc \
    && . ~/.bashrc && anyenv install phpenv \
    && . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --with-pdo-pgsql --with-pgsql --with-freetype=/usr/include/freetype2/freetype --with-imap=/usr/lib64/ --with-imap-ssl --with-kerberos --enable-opcache" phpenv install -v $INSTALL_PHP_VERSION \
    && . ~/.bashrc && phpenv rehash

# -- copy modules & misc
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules \
    && \cp -f /etc/httpd/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules \
    && mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && \cp -f /usr/lib64/libzip.so* /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && \cp -f /usr/lib64/libonig.so* /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/lib64_modules \
    && echo 'sendmail_path = /usr/sbin/sendmail -t -i' >> /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/etc/php.ini

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
