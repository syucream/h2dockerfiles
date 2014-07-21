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
# Install nginx to test trafficserver's HTTP/2 proxy features
#
RUN apt-get install -y nginx

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

#
# Install packages required by trafficserver
#
RUN apt-get install -y             \
            libmodule-install-perl \
            g++                    \
            libssl-dev             \
            tcl-dev                \
            expat                  \
            libexpat-dev           \
            libpcre3-dev

RUN ldconfig


#
# Prepare HTTP server as origin server of trafficserver
#
RUN mkdir /opt/htdocs
RUN echo "TEST" > /opt/htdocs/index.html

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
    ./configure --enable-app && \
    make &&                     \
    make install

#
# Build and install trafficserver applied the http2 patch
#
RUN git clone https://github.com/apache/trafficserver.git
ADD ./http2-0004.patch /opt/trafficserver/http2.patch
RUN sed -i -s "s/h2-12/h2-13/" /opt/trafficserver/http2.patch
RUN cd trafficserver &&                                              \
    git checkout -b h2ts 43be06849b560b62ae16d1c3f450c860ac1bbe0d && \
    patch -p1 < http2.patch &&                                       \
    autoreconf -if &&                                                \
    ./configure --enable-http2 &&                                    \
    make &&                                                          \
    make install

RUN ldconfig

#
# Configure trafficserver as proxy server of nginx
#
RUN echo "map / http://127.0.0.1/" >> /usr/local/etc/trafficserver/remap.config
