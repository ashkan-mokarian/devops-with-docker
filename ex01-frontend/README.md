# Description

Exercise project; containerizing a frontend on port, as described in [here](https://devopswithdocker.com/part-1/section-6)

# Steps

1. Read the frontend application README [here](https://github.com/docker-hy/material-applications/tree/main/example-frontend) and write the Dockerfile. The Solution to the Dockerfile is [here](./Dockerfile).

2. Make sure to copy the app to Docker context root `cp -r ../material-applications/example-frontend .`

2. While inside the ex01-frontend directory, run the command `docker build . -t frontend`.

3. To run: `docker run -d -p 5000 frontend`

4. check `docker ps` for the exposed port on host machine, e.g. 52286

5. In the browser, go to `localhost:52286`.