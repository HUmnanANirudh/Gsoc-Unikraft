FROM curlimages/curl:latest AS build

FROM scratch

COPY --from=build /usr/bin/curl /usr/bin/curl

COPY --from=build /lib/ /lib/
COPY --from=build /usr/lib/ /usr/lib/
COPY --from=build /etc/ssl/ /etc/ssl/
COPY --from=build /etc/nsswitch.conf /etc/nsswitch.conf

CMD ["/usr/bin/curl"]