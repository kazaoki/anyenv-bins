FROM centos:7

ENV INSTALL_PHP_VERSION="5.3.29"

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
        freetype-devel

# -- autoconf 2.59
RUN yum install -y perl-Data-Dumper \
        && mkdir -p /opt/src/autoconf-2.59 \
        && cd /opt/src/ \
        && curl -O http://ftp.gnu.org/gnu/autoconf/autoconf-2.59.tar.gz \
        && tar xozf autoconf-2.59.tar.gz \
        && cd autoconf-2.59 \
        && /opt/src/autoconf-2.59/configure \
        && make \
        && make install

# -- phpenv setup
RUN git clone https://github.com/riywo/anyenv ~/.anyenv
RUN echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc
RUN /root/.anyenv/bin/anyenv install --force-init
RUN echo 'eval "$(anyenv init -)"' >> ~/.bashrc
RUN . ~/.bashrc && anyenv install phpenv
RUN . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs --disable-fpm --with-pdo-pgsql --with-pgsql --with-freetype-dir=/usr/include/freetype2/freetype" phpenv install -v $INSTALL_PHP_VERSION
RUN . ~/.bashrc && phpenv rehash

# -- copy httpd module
RUN mkdir /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN \cp -f /etc/httpd/modules/libphp*.so /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/httpd_modules
RUN echo 'sendmail_path = /usr/sbin/sendmail -t -i' >> /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION/etc/php.ini

# -- staging
FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
VOLUME /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION
