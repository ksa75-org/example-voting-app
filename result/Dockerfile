FROM node:8.9-alpine

RUN mkdir -p /app
WORKDIR /app

RUN export http_proxy="http://172.17.7.137:3129/" \
    && export https_proxy="http://172.17.7.137:3129/" \
    && npm install -g nodemon
RUN npm config set registry https://registry.npmjs.org
COPY package.json /app/package.json
RUN export http_proxy="http://172.17.7.137:3129/" \
    && export https_proxy="http://172.17.7.137:3129/" \
    && npm install \
 && npm ls \
 && npm cache clean --force \
 && mv /app/node_modules /node_modules
COPY . /app

ENV PORT 80
EXPOSE 80

CMD ["node", "server.js"]
