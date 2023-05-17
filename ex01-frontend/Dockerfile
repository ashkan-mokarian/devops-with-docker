FROM ubuntu:16.04

EXPOSE 5005

WORKDIR /usr/src/app

RUN apt update && apt install -y curl

RUN curl -sL https://deb.nodesource.com/setup_16.x | bash

RUN apt install -y nodejs

COPY example-frontend .

# This is regarding exercise 1.14
ENV REACT_APP_BACKEND_URL http://localhost:8080/

RUN npm install
RUN npm run build
RUN npm install -g serve

CMD ["serve", "-s", "-l", "5005", "build"]