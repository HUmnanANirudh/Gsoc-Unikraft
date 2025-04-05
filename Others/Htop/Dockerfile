FROM alpine:latest AS builder

RUN apk add --no-cache htop ncurses-libs

RUN mkdir -p /rootfs/usr/bin /rootfs/usr/lib

RUN cp /usr/bin/htop /rootfs/usr/bin/
RUN cp /lib/ld-musl-x86_64.so.1 /rootfs/usr/lib/
RUN cp /usr/lib/libncursesw.so.6 /rootfs/usr/lib/
RUN cp /usr/lib/libtinfo.so.6 /rootfs/usr/lib/