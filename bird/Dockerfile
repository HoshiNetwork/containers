ARG alpinever
ARG busyboxver

FROM alpine:${alpinever} AS builder

ARG pkgver

RUN apk add --no-cache \
            bison \
            curl \
            flex \
            gcc \
            gzip \
            linux-headers \
            make \
            musl-dev \
            ncurses-dev \
            readline-dev

WORKDIR /root
RUN set -ex; \
        tarball="https://bird.network.cz/download/bird-${pkgver}.tar.gz"; \
        curl -fL -o bird.tar.gz $tarball; \
        mkdir -p /build/bird /src/bird; \
        tar -zxf bird.tar.gz -C /src/; \
        rm bird.tar.gz; cd /src/bird-${pkgver}; \
        ./configure \
            --prefix=/usr \
            --sbindir=/usr/bin \
            --sysconfdir=/etc \
            --localstatedir=/var \
            --runstatedir=/run/bird \
            --docdir=/usr/share/man && \
        make && \
        make DESTDIR=/build/bird install

FROM busybox:${busyboxver}-musl AS target

MAINTAINER Allen Zhong <pdev@zhoal.pw>
LABEL org.opencontainers.image.source=https://github.com/HoshiNet/containers

COPY --from=builder /build/bird /
COPY --from=builder /lib/ld-musl-x86_64.so* /lib/
COPY --from=builder /usr/lib/libreadline.so* /usr/lib/
COPY --from=builder /usr/lib/libncursesw.so* /usr/lib/

EXPOSE 179
CMD ["/usr/bin/bird", "-f", "-c", "/etc/bird.conf"]

