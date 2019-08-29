FROM debian:stretch-slim AS builder
RUN apt-get update && apt-get install -y gcc pkg-config automake autoconf libtool make libssl-dev libcurl4-openssl-dev
WORKDIR /build
COPY . .
RUN ./autogen.sh && ./configure && make

FROM debian:stretch-slim
RUN  apt-get update && apt-get -y install curl
COPY --from=builder /build/osslsigncode /bin/osslsigncode
ENTRYPOINT [ "/bin/osslsigncode" ]