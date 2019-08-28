FROM alpine:3 AS builder
RUN apk add --update gcc automake autoconf libtool make openssl-dev musl-dev curl-dev
WORKDIR /build
COPY . .
RUN ./autogen.sh && ./configure && make

FROM alpine:3
RUN apk add --update curl
COPY --from=builder /build/osslsigncode /bin/osslsigncode
ENTRYPOINT [ "/bin/osslsigncode" ]