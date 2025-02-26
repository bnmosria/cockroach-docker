# Stage 1 - generate certs
FROM cockroachdb/cockroach:latest-v22.1 AS generator

LABEL maintainer="artemervits at gmail dot com"
LABEL version="6.0"
LABEL description="cockroach cert generator container"
ENV REFRESHED_AT 2022_06_09

RUN mkdir -pv /tmp/certs/ca /tmp/certs/roach-0 /tmp/certs/roach-1 /tmp/certs/roach-2 /tmp/safe /tmp/certs/web /tmp/certs/lb

RUN cockroach cert create-ca --certs-dir=/tmp/certs/ca --ca-key=/tmp/safe/ca.key \
    && cp -v /tmp/certs/ca/ca.crt /tmp/certs/web \
    && cp -v /tmp/certs/ca/ca.crt /tmp/certs/roach-0 \
    && cp -v /tmp/certs/ca/ca.crt /tmp/certs/roach-1 \
    && cp -v /tmp/certs/ca/ca.crt /tmp/certs/roach-2 \
    && cp -v /tmp/certs/ca/ca.crt /tmp/certs/lb

RUN ./cockroach cert create-client root --certs-dir=/tmp/certs/web --ca-key=/tmp/safe/ca.key --also-generate-pkcs8-key
RUN ./cockroach cert create-client roach --certs-dir=/tmp/certs/web --ca-key=/tmp/safe/ca.key --also-generate-pkcs8-key

RUN cp -v /tmp/certs/web/client.* /tmp/certs/roach-0 \
    && cp -v /tmp/certs/web/client.* /tmp/certs/roach-1 \
    && cp -v /tmp/certs/web/client.* /tmp/certs/roach-2 \
    && cp -v /tmp/certs/web/client.* /tmp/certs/lb

RUN cockroach cert create-node roach-0 lb --certs-dir=/tmp/certs/roach-0 --ca-key=/tmp/safe/ca.key
RUN cockroach cert create-node roach-1 lb --certs-dir=/tmp/certs/roach-1 --ca-key=/tmp/safe/ca.key
RUN cockroach cert create-node roach-2 lb --certs-dir=/tmp/certs/roach-2 --ca-key=/tmp/safe/ca.key
RUN cockroach cert create-node lb --certs-dir=/tmp/certs/lb --ca-key=/tmp/safe/ca.key

# Stage 2 - share certs

FROM alpine:3.12

RUN mkdir -pv /certs/roach-0 /certs/roach-1 /certs/roach-2 /certs/web /certs/lb

COPY --from=generator  /tmp/certs/roach-0/* /certs/roach-0/
COPY --from=generator  /tmp/certs/roach-1/* /certs/roach-1/
COPY --from=generator  /tmp/certs/roach-2/* /certs/roach-2/
COPY --from=generator  /tmp/certs/web/* /certs/web/
COPY --from=generator /tmp/certs/lb/* /certs/lb/

CMD tail -f /dev/null
