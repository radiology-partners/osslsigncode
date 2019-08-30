FROM debian:unstable-slim AS builder
#Build openssl
RUN apt-get update && apt-get install -y make gcc git wget autoconf libtool gtk-doc-tools autopoint glib2.0-dev libxml2-dev liblzma-dev libstdc++-9-dev
WORKDIR /build
RUN git clone https://github.com/openssl/openssl.git && cd openssl && git fetch \
    && git fetch --tags && git checkout OpenSSL_1_1_1c
WORKDIR /build/openssl
RUN ./config --prefix=/dist/openssl && export CFLAGS="-static -static-libgcc" \
    && mkdir -p /dist/openssl && make install

#Build zlib
WORKDIR /build
RUN wget https://www.zlib.net/zlib-1.2.11.tar.gz && tar -xvf zlib-1.2.11.tar.gz \
    && mv zlib-1.2.11 zlib
WORKDIR /build/zlib
RUN ./configure --prefix=/dist/zlib && make && make install

#Build curl
WORKDIR /build
RUN git clone https://github.com/curl/curl.git && cd curl && git fetch \
    && git fetch --tags && git checkout curl-7_65_3
WORKDIR /build/curl
RUN ./buildconf && ./configure --prefix=/dist/curl --with-ssl=/dist/openssl --with-zlib=/dist/zlib && make && make install

#Build libgsf
WORKDIR /build
RUN git clone https://github.com/GNOME/libgsf.git && cd libgsf && git fetch \
    && git fetch --tags && git checkout LIBGSF_1_14_46
WORKDIR /build/libgsf
RUN ./autogen.sh && ./configure --prefix=/dist/libgsf && make && make install

#Build osslsigncode statically
WORKDIR /build/osslsigncode
COPY . .
RUN export LIBRARY_PATH=/dist/openssl/lib:/dist/libgsf/lib:/dist/zlib/lib \
    && export PKG_CONFIG_PATH=/dist/openssl/lib/pkgconfig:/dist/libgsf/lib/pkgconfig:/dist/curl/lib/pkgconfig:/dist/zlib/lib/pkgconfig \
    && export PKG_CONFIG="pkg-config --static" \
    && export LDFLAGS=-static \
    && find /dist -name "*.so*" -delete \
    && ./autogen.sh && ./configure && make

FROM alpine:3
COPY --from=builder /build/osslsigncode/osslsigncode /bin/osslsigncode
ENTRYPOINT [ "/bin/osslsigncode" ]