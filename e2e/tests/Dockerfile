FROM node:8.16.0-jessie-slim

RUN export http_proxy="http://172.17.7.137:3129/" \
    && export https_proxy="http://172.17.7.137:3129/" \
    && apt-get update -qq && apt-get install -qy \ 
    ca-certificates \
    bzip2 \
    curl \
    libfontconfig \
    --no-install-recommends
RUN http_proxy="http://172.17.7.137:3129/" yarn global add phantomjs-prebuilt
ADD . /app
WORKDIR /app
CMD ./tests.sh
