# Getting Started with Docker React Node App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `yarn start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `yarn test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `yarn build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `yarn eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `yarn build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)

### Dockerfile instructions:
````
FROM         # to specify the base image 
WORKDIR      # to set the working directory
COPY         # to copy files/directories
ADD          # to copy files/directories
RUN          # to run commands
ENV          # to set environment variables
EXPOSE       # to document the port the container is listening on
USER         # to set the user running the app
CMD          # to set the default command/program
ENTRYPOINT   # to set the default command/program
````

### Build the app using docker:
````
docker build -t react-app .
docker run react-app sh -> run container in shell mode
docker run react-app
````

#### Remove docker images from a machine:
````
docker image prune
````
````
docker ps
docker ps -a
docker container prune
docker image prune
````
At this point all images with unknown tags will be deleted.
to remove all stopped and labelled images
````
docker ps -a
docker container rm -f <CONTAINER_ID>
docker image rm <IMAGE_ID>

docker image rm -f $(docker image ls -aq) <- remove all images from machine
````

####Tagging docker images:
````
docker build -t react-app:<TAG_NAME(could be release name of version)> .
docker build -t react-app:v1 .

or after the build

docker image tag react-app:latest react-app:v1

````
sometimes if there is a situation with same image but multiple tags
````
docker image remove <REPO_NAME>:<TAG_NAME>
docker image remove react-app:v1

tag a version to latest

docker image <IMAGE_ID> react-app:latest
````

#### publish docker image to dockerhub:
````
1. initially create a repository in hub.docker.com
2. from cmd -> docker image tag <IMAGE_ID> premsudheep/react-app:<EXPLICIT_TAG_NAME>
3. docker login
4. docker push premsudheep/react-app:<EXPLICIT_TAG_NAME>
````

#### Saving and loading docker images:
````
1. docker image save -o react-app.tar react-app:<TAG_NAME>
2. docker image load -i react-app.tar
````

### Containers:

start container:
````
docker ps <- gives all running containers
docker ps -a <- gives all running + stoppped containers
docker run -d react-app <- runs container in a detached mode(shell/bash will be detatched but container runs in background)
docker run -d --name blue-sky react-app <- runs container in a detached mode with a name
````
viewing running detached container logs:
````
docker ps
docker logs <CONTAINER_ID>
docker logs --help
docker logs -n 100 -t <CONTAINER_ID> <- prints las 100 lines with timestamps
````
running a container on a specific port:
````
docker run -d -p 80:3000 --name blue-sky react-app <- this can be accessable from localhost:80(host) which internally calls the container running on port 3000 
````
exec commands on a running container:
````
docker exec blue-sky ls -ltr
docker exec blue-sky whoami
docker exec -it blue-sky sh <- you can all "exit" to close the shell which will just close the shell but won't end the container
````
stopping and staring container:
````
docker stop blue-sky
docker start blue-sky <-with "docker run" we start a new container and "docker start" we start a stopped container
````
removing container:
````
docker container rm -f blue-sky
or
docker rm -f blue-sky
or
docker ps -a | grep blue-sky

docker container rm -f $(docker container ls -aq) <- remove all conatiners
````
Container File System:

each container has its own FS which is invisible to the other container.
we shouldn't store data in an FS. That's where we have a concept called volumes.
A Volume is a storage outside of containers. directly on the host or somewhere on the cloud
````
docker volume create app-data
docker vokume inspect app-data

docker run -d -p 4000:3000 -v app-data:/app/data react-app <- this is create a volume in athe /app/data dir 
````
Copying files b/w the host and containers:
````
docker exec -it <CONTAINER_ID> sh
echo hello > log.txt
exit

docker cp <CONTAINER_ID>:/app/log.txt .
ls -ltr

echo hello > secret.txt
docker cp secret.txt <CONTAINER_ID>:/app
docker exec -it <CONTAINER_ID> sh
ls -ltr
````
Sharing source code with a container:
````
docker run -d -p 5001:3000 -v $(pwd):/app react-app
docker logs -f <CONTAINER_ID>
````

### Docker compose
compose multi container app
find the docker-compose.yml file path and run:
````
docker-compose up
````

#### Build images using docker-compose 
similar to all commands under "docker CMD>" we use same to "docker-compose CMD" which will apply to the whole (multi containers)

Refer to:
````
docker-compose build --help
    --no-cahce
    --pull
````
build:
````
docker-compose build
docker-compose build --nocache
````
starting and stopping applications(containers):
````
docker-compose up -d
then docker-compose ps (to verify)
and the localhost:3000 (to run)

docker-compose down
````
Docker network:
the web,api,db containers talk to each other using a network
each container has a IP address
and each container has a DNS Resolver that talks to a DNS Server in the host to get the IP of another container for communication. 
````
docker-compose up -d
docker network ls -ltr
docker exec -it -u root <CONTAINER_ID> sh
# ping api
# ping web
````
Viewing Logs:
````
docker-compose logs
docker-compose logs -f -t
docker logs <CONTAINER_ID> -f
````

### Deployment of Applications
- single host deployment
- cluster deployment

Docker Swarm - Orchestration tool for a docker (similar to kubernetes)

VPS(Virtual Private Server)
- Digital Ocean
- Google Cloud Platform(GCP)
- Microsoft Azure
- Amazon Web Services(AWS)

Installing docker-machine(docker engine) in the host machine
````
https://github.com/docker/machine
````

Creating VPS on Digital Ocean using docker machine
This will create a VM and install docker engine using docker machine remotely on our VM
````
docker-machine create \
--driver digitalocean \
--digitalocean-access-token <GENERATED_ACCESS_TOKEN_FROM_DIGIOCEAN> \
--engine-install-url "https://release.rancher.com/install-docker/19.03.9.sh" \
docker-react-node
````
````
docker-machine ls
docker-machine ssh docker-react-node
root@docker-react-node:~# ls -ltr
````

Run from fontend app path(this will reduce the image size)
````
docker build -t react-node_web_opt -f Dockerfile.prod .
````

Now run from docker-compose path
````
docker-compose -f docker-compose.prod.yml build
docker images
````

#### deployment of app
````
docker-machine ls
docker-machine env <MACHINE_NAME>
eval $(docker-machine env <MACHINE_NAME>)
docker images
docker-compose -f docker-compose.prod.yml up -d --build
````
