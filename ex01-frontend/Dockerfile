FROM ubuntu:16.04

EXPOSE 3000

WORKDIR /usr/src/app

RUN apt update && apt install -y curl

RUN curl -sL https://deb.nodesource.com/setup_16.x | bash

RUN apt install -y nodejs

COPY example-frontend .

RUN npm install
RUN npm run build
RUN npm install -g serve

CMD ["serve", "-s", "-l", "5000", "build"]