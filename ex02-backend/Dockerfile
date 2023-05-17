FROM ubuntu:16.04

EXPOSE 8080

WORKDIR /usr/src/app

RUN apt-get update && apt-get install -y wget

RUN wget https://go.dev/dl/go1.20.4.linux-amd64.tar.gz

RUN tar -C /usr/local -xzf go1.20.4.linux-amd64.tar.gz

# Set the PATH variable to include Go binary
ENV PATH="${PATH}:/usr/local/go/bin"

COPY example-backend .

# This is regarding exercise 1.14
ENV REQUEST_ORIGIN http://localhost:5005

RUN go build

CMD ["./server"]