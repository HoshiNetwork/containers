MAINTAINER Allen Zhong <pdev@zhoal.pw>
LABEL Description="Bird Internet routing daemon docker image" Vendor="Hoshi Network"

ENV BIRD_VER 2.15.1

FROM alpine:3 AS builder

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
        tarball="https://bird.network.cz/download/bird-${BIRD_VER}.tar.gz"; \
        curl -fL -o bird.tar.gz $tarball; \
        mkdir -p /build/bird /src/bird; \
        tar -zxf bird.tar.gz -C /src/; \
        rm bird.tar.gz; cd /src/bird-${BIRD_VER}; \
        ./configure \
            --prefix=/usr \
            --sbindir=/usr/bin \
            --sysconfdir=/etc \
            --localstatedir=/var \
            --runstatedir=/run/bird \
            --docdir=/usr/share/man && \
        make && \
        make DESTDIR=/build/bird install

FROM busybox:musl AS target
COPY --from=builder /build/bird /
COPY --from=builder /lib/ld-musl-x86_64.so* /lib/
COPY --from=builder /usr/lib/libreadline.so* /usr/lib/
COPY --from=builder /usr/lib/libncursesw.so* /usr/lib/

EXPOSE 179
CMD ["/usr/bin/bird", "-f", "-c", "/etc/bird.conf"]

