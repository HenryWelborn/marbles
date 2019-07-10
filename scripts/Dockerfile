FROM node:8.15.1
COPY . /app
WORKDIR /app
RUN rm -rf node_modules && npm install && npm install -g gulp
CMD gulp marbles_tls
