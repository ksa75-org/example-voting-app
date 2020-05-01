FROM alpine:3.9.4
WORKDIR /test
COPY . .
RUN export http_proxy="http://172.17.7.137:3129/" \
    && export https_proxy="http://172.17.7.137:3129/" \
    && apk add curl
CMD sh
