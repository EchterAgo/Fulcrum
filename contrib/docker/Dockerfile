FROM alpine:3.11.2
LABEL maintainer="Axel Gembe <derago@gmail.com>"

RUN apk add --no-cache qt5-qtbase openssl libbz2 zlib && \
    apk add --no-cache git build-base qt5-qtbase-dev bash

RUN mkdir rocksdb && cd rocksdb && git init && \
    git remote add origin https://github.com/facebook/rocksdb.git && \
    git fetch --depth 1 origin v6.5.2 && \
    git checkout -b pinned FETCH_HEAD && \
    sed -i -e 's/install -C/install -c/' Makefile

RUN cd rocksdb && \
    USE_RTTI=1 PORTABLE=1 make shared_lib && \
    INSTALL_PATH=/usr make install-shared

RUN mkdir Fulcrum && cd Fulcrum && git init && \
    git remote add origin https://github.com/cculianu/Fulcrum.git && \
    git fetch --depth 1 origin master && \
    git checkout -b pinned FETCH_HEAD

RUN cd Fulcrum && \
    qmake-qt5 -makefile features= Fulcrum.pro

RUN cd Fulcrum && \
    make install

RUN apk del git build-base qt5-qtbase-dev bash && \
    rm -rf rocksdb Fulcrum

VOLUME ["/data"]
ENV DATA_DIR /data

ENV SSL_CERTFILE ${DATA_DIR}/fulcrum.crt
ENV SSL_KEYFILE ${DATA_DIR}/fulcrum.key

EXPOSE 50001 50002

COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

CMD ["Fulcrum"]
