FROM ubuntu
MAINTAINER syu_cream
WORKDIR /opt

#
# Prepare build environment
#
RUN apt-get -y update
RUN apt-get install -y       \
            git              \
            build-essential  \
            autoconf         \
            automake         \
            autotools-dev    \
            libtool          \
            pkg-config

#
# Install packages required by nghttp2
#
RUN apt-get install -y       \
            zlib1g-dev       \
            libcunit1-dev    \
            libssl-dev       \
            libxml2-dev      \
            libevent-dev     \
            libjansson-dev   \
            libjemalloc-dev  \
            cython           \
            python3.4-dev

RUN ldconfig


#
# Build and install latest opnessl
#
RUN git clone --depth 1 https://github.com/openssl/openssl.git
RUN cd openssl &&           \
    ./config &&             \
    make &&                 \
    make install

#
# Build and install latest nghttp2
#
RUN git clone --depth 1 https://github.com/tatsuhiro-t/nghttp2.git
RUN cd nghttp2 &&               \
    autoreconf -if &&           \
    ./configure &&              \
    make &&                     \
    make install

#
# Build and install latest curl
#
RUN git clone --depth 1 https://github.com/bagder/curl.git
ENV PKG_CONFIG_PATH /usr/local/lib/pkgconfig
RUN cd curl &&                                           \
    autoreconf -if &&                                    \
    ./configure --with-nghttp2=/usr/local/lib/nghttp2 && \
    make &&                                              \
    make install

RUN ldconfig
