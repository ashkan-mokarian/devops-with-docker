# Description

Exercise project; containerizing the backend, as described in [Mandatory Exercise 1.13: HELLO, BACKEND!](https://devopswithdocker.com/part-1/section-6)

# Run steps

1. Read the backend application README [here](https://github.com/docker-hy/material-applications/tree/main/example-backend) and write the Dockerfile. The Solution to the Dockerfile is [here](./Dockerfile).

2. Make sure to copy the app to Docker context root `cp -r ../material-applications/example-backend .`

2. While inside the ex02-backend directory, run the command `docker build --platform linux/amd64 . -t backend`. '--platform' needed as explained [here](../README.md#building-on-arm-architectures-m1).

3. To run: `docker run -d -p 8080 backend`

4. check `docker ps` for the exposed port on host machine, e.g. 52286

5. In the browser, go to `localhost:52286/ping` and should get 'pong'.

# To run with frontend
make sure that the ports are predefined. since communication is on host, and if random ports are assigned, the dockerfile and the built images are not configured to work with such random ports. fix the ports as used in both the docker files, and run both containers side by side, and it should work. 