FROM centos:7

ENV INSTALL_PHP_VERSION="5.2.17"

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
        mariadb-devel
RUN git clone https://github.com/riywo/anyenv ~/.anyenv
RUN echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bashrc
RUN echo 'eval "$(anyenv init -)"' >> ~/.bashrc
RUN . ~/.bashrc && anyenv install phpenv
RUN . ~/.bashrc && CONFIGURE_OPTS="--with-apxs2=/usr/bin/apxs" phpenv install $INSTALL_PHP_VERSION

FROM busybox
COPY --from=0 /root/.anyenv/envs/phpenv/versions/$INSTALL_PHP_VERSION /anyenv/phpenv/versions/$INSTALL_PHP_VERSION
