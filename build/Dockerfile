FROM debian:unstable-slim AS builder
RUN apt-get update && apt-get install -y gcc pkg-config automake autoconf libtool make libssl1.1 libssl-dev libcurl4-openssl-dev
WORKDIR /build
COPY . .
RUN ./autogen.sh && ./configure && make

FROM debian:unstable-slim
RUN  apt-get update && apt-get -y install curl libssl1.1
COPY --from=builder /build/osslsigncode /bin/osslsigncode
ENTRYPOINT [ "/bin/osslsigncode" ]