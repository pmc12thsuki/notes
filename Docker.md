DOCKER 
===
###### tags: `DevOps`

Basic
===

- creating and running a container from an image
```shell=
docker run <image name>
#example
docker run hello-world

docker run <image name> = docker create <image name>(create a container) + docker start <image name>(start a container)
#example
docker create hell-world
# will get container id
docker start -a <container id> #-a will print output
# can use docker start to restart a finished or stopped container
```
- other command
```shell=
doker run <image name> <other commange>

#example
docker run busybox echo hi there
docker run busybox ls
```
- list running container
```shell=
docker ps
```

- list all container that have been run in our machine
```shell=
docker ps --all
```
- removing stopped containers
```shell=
docker system prune
# do it when everytime finish a job
```
- log out message after the container exit
```shell=
docker logs <container id>
# will not rerun the program, only log out the message that have been emitted
```

- stop a running container
```shell=
docker stop <container id>
# give this container some time to shutdown, better to use

docker kill <container id>
# immediately shutdown this container
```

- executing command in running container
```shell=
docker run redis (run a redis-server in docker)
# if you open another terminal and type redis-cli, wont work because this command cant talk to docker(docker is a isolate env)
# so we need the command below
# docker exec -it <container id which we want to interact with> <command>
# it means can input value
docker exec -it <container id of redis-server> redis-cli
```
- the -it flag: i will connet terminal and container a STDIN(standard in, input), t will give a pretty output to terminal

- executing command in running container in **another terminal**
```shell=
#open another terminal window
docker exec -it <container id which we want to interact with> <command processors>

# example
docker exec -it <id> sh # most often
docker exec -it <id> bash # if bash can use in this container
docker exec -it <id> zsh # if zsh can use in this container
=
```



- executing command in running container in **same terminal**
```shell=
#example
docker run -it <container> <command processors>
docker run -it busybox sh
```
- If you run two container(even from the same image), the two container have absolutely completey separate file system and there's no sharing of data between the two

Create a Image
===
- write a docker file to specifu how to create a image
- flow :
1. Specify a base image
2. run some commands to install additional programs
3. specify a command to run on continer startup (ex run redis_cli)

```dockerfile=
# use an existing docker image as a base
# use an base image from alpine
FROM alpine

#Download and install a dependency
RUN apk add --update redis

# tell the image what to do when it starts as a container
# use double quote in docerfile
CMD ["redis-server"]
```
4. run in shell to create image
```shell=
docker build . 
```

5. add tags on self-build image:
```shell=
docker build -t <docker_user/image_name:version> .
#example
#the latest tag can be omit 
docker build -t pmc12thsuki/someImage:latest .
#then you can run the image with tag
docker run pmc12thsuki/someImage 
# docker run : create and start a container
# if run the lastest version, dont need to specify version number
```

Run simple node server in docker
===
1. build a server, do not include the node_module in it
2. build Dockerfile(with some mistake)
```dockerfile=
# base image, cant use alpine because it dont have node and npm pre-installed. we can fix it by install some other base image or add npm into alpine by ourself. in here, we pick another base image which have node installed.
FROM node:alpine

# install some dependencies
RUN npm install

# default command
CMD ["npm", "start"]
```
> **alpine** means a term in docker world for a image that is as **small and compact as possible**
> Many repository offer alpine version of their image

3. because package.json and server.js is not seen by our base image, so when we run npm install , it will through an error which says package.json not found. to fix this, we should let our container aware of our code.

4. modify our dockerfile (with some mistake) 

```dockerfile=
# use COPY <path to folder to copy on your machine relative to the build context> <Place to copy stuff to inside the container>

# base image
FROM node:alpine

#any following command will be executed relative to this path in the container. we dont want to copy our code into the root dir of base image
WORKDIR /usr/app

# install some dependencies
# copy package.json and index.js to our temp container which is build form our base image

COPY ./ ./
RUN npm install

# default command
CMD ["npm", "start"]
```

5. the image will buld successfully. but after we use "docker run" to create and run this container, the coming request cant send to our container, because container **can not** reach form outside. to fix it, we need to specify our **port mapping** to let incoming req go to our container
>specify our **port mapping** to let incoming req go to our container
6. use below command 
```shell=
#Route incoming request to this port on localhost to port inside container port. 
#this two port do not have to be identical
docker run -p <localhost's port> : <port inside container>
```

7. modifiy docker file to minimize rebuild
```dockerfile=
# base image
FROM node:alpine

WORKDIR /usr/app

# install some dependencies
# copy only package.json first, so if we only change our server.js file , we dont need to npm install again
COPY ./package.json ./ 
RUN npm install
COPY ./ ./

# default command
CMD ["npm", "start"]
```

Docker Compose (Multiple local containers connection)
===
- if we want to use redis(redis-cli) in our server, we need to run redis-server, too.
- docker compose can be used to start up **multiple Docker container** at the same time
- write docker-compose.yml file to run automate script. (we dont need to type long command in shell)
- create docker-compose.yml
> - docker-compose.yml file is used to specify how to create and start our container
> - Dockerfile is used to specify how to create our image
```yaml=
version : '3' # the version of docker-compose

services: # here are the containers i want to create, the created container are in the same network and can communicate to each other
  redis-server: # i want to create a service called redis-server
    image: 'redis' # make it using 'redis' image
  node-app: # i want to create a service called node-app
    build: . # make it by creating a image using the dockerfile inside this dir
    ports: # mapping ports 
      - "3000:8080" # mapping local_machine's port 3000 to container's port 8080 (can mapping serveral port, so is a array type)
```

- in server.js file, create a redis-client :
```javascript=
const redis = require('redis');
const redisClient = redis.createClient({
    host: 'redis-server',  //use the name we specify in our docker-compose's services. node will think redis-server is a host name and try to connect, and docker will see it and create a connection between node and redis-server
    port: 6379
});
```
- run docker-compose's command to build app
(left is the old command without docker-compose, right is the new command)
![](https://i.imgur.com/N2eorND.png)

![](https://i.imgur.com/Nbxuy2e.png)

## docker compose restart policy
- add restart in docker-compose to automatically restart. 
- exit(0) is normally exit, others number are error code
![](https://i.imgur.com/O8pk042.png)
>"no" is stringa, but others are not. because no is a key word in yml file

modify docker-compose flie to set a restart policy
```yaml=
node-app: # i want to create a service called node-app
    restart: always # always restart this server if it is crashed for any reason. usually used in web server.
    build: . # make it by creating a image using the dockerfile inside this dir
    ports: # mapping ports 
      - "3000:8080" # mapping local_machine's port 3000 to container's port 8080 (can mapping serveral port, so is a array type)
```

- see running container by docker-compose
```shell=
docker-compose ps
```
Work flow with Docker
===
![](https://i.imgur.com/VwORJeZ.png)
> Docker is a **tool** in a normal development workflow. It is not necessary but can make our life easier

Create an react app Container
===

## Development
- build a Dockerfile.dev for development
```dockerfile=
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "start"]
```
- run command to build a development image by Dockerfile.dev
```shell=
docker build -f Dockerfile.dev .
```
> do not build with node_module in the folder, this will make build take longer and larger

### Docker volumes
- 原本每寫完一個版本的code都要用 docker build 一次，因為每次 build image 時都會把當時資料夾裡的 code 複製到 image 中，所以 docker 會是那時的 snapshot
- 利用 docker volumes，我們將要搬到 container 中的資料夾用 reference 指導我們的工作資料夾中，這樣就不用每次改寫 code 都要重新 build image。
> 利用 Docker volume 達到類似 hot reload 的功能
> 
![](https://i.imgur.com/9HGtCUN.png)
- 若要用 docker volume ， 則需要使用複雜的 docker run command
```shell=
docker run -p port:port -v /app/node_modules -v$(pwd):/app <image_id>
# -v /app/node_modules ：不要 map node_module 資料夾，因為當前資料夾中沒有這個資料夾
# -v$(pwd):/app ：把所有當前資料夾的東西 map 到 /app(container)中
```
- 但這個 command 太長了，所以使用 docker-compose 來解決
```yaml=
version: '3'

services: 

  web:
    # 因為資料夾裡目前沒有 Dockerfile，只有 Dockerfile.dev，所以要特別指定 context 跟 dockerfile
    build:
      # pull all file to container
      context: .
      # location of dockerfile
      dockerfile: Dockerfile.dev

    ports:
      - "3000:3000"

    volumes:
      # do not try to map this folder (node_module) inside container
      - /app/node_modules
      # map the folder (outside the container) to the folder inside the container
      - .:/app
```

## Testing


- 想要 test file 也可以參照到我們的資料夾（docker volume），有兩個方案：
1. 用 docker-compose up 來 run container，再另外開一個 terminal 來對已經在運行的 container 下達 npm test 的指令 （docker exec -it <runnind_container_id> npm test），如此可以透過第二個視窗對 test 進行溝通
3. 可以再起一個 services(container) ， 這樣只要下達 docer-compose up，就會自動運行兩個 services (app and test)
```yaml=
version: '3'

services: 

  web:

    build:
      # pull all file to container
      context: .
      # location of dockerfile
      dockerfile: Dockerfile.dev

    ports:
      - "3000:3000"

    volumes:
      # do not try to map this folder (node_module) inside container
      - /app/node_modules
      # map the folder (outside the container) to the folder inside the container
      - .:/app

  #create another container to run test solely
  test: 

    build:
      # pull all file to container
      context: .
      # location of dockerfile
      dockerfile: Dockerfile.dev

    volumes:
      # do not try to map this folder (node_module) inside container
      - /app/node_modules
      # map the folder (outside the container) to the folder inside the container
      - .:/app
    
    #overwrite command
    command: ["npm", "test"]
```

## Production
- 下達 npm run build 後 react app 就會 build 出適合 production 跑的資料，都放在 build 資料夾中。其中.js file 都被集合、且拿掉 node_module file，變成一個常見的 static file。
- 在我們的 production container 中，並不需要包含 build 資料夾以外的所有東西，只需要把 build 資料夾包含進去就好
- 要創造 react app 的 production 時，因為這個 app 缺少 Production server，所以需要使用 nginx server 來處理 request and response react build 出來的 static file
> use ngnix to host some simple static content


### Multi-step Docker Builds
![](https://i.imgur.com/dl4Yr4g.png)
> have multi base images !

- create a multi step Dockerfile
```dockerfile=
# first phase , to build the react app
FROM node:alpine as builder

WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
# dont need to run this container, so just run npm build, no need to specify CMD
RUN npm run build

# after the first step, we have all the stuff we need in /app/build

# second phash, to run the app
FROM nginx
# copy file from different phase, see the ngnix document, it tells us to copy our static file into usr/share//nginx/html
COPY --from=builder /app/build /usr/share/nginx/html
# the default nginx start CMD will start image for us, dont need to specify one
```

- build the image and run the container, then the react app can run as production 
```shell=
docker build .
docker run -p 8080:80 <container id>
# the default port of ngnix is 80
```

Travis CI
===
- can be used to test and deploy code
- travis does work based on the .travis.yml file
## test
```yaml=
# tell travis we need sudo permission to run docker
sudo: required

# need docker-cli install to test our app
services: 
  - docker

# before test our app, need to run some command (eg: build our docker image)
before_install:
  - docker build -t pmc12thsuki/docker-react -f Dockerfile.dev .

# script need to contain all the command that we need
script: 
  # a script to run our test, -- --coverage make test exit after the test is finished. otherwise the test will never finished and Travis will wait
  # travis will assume that if exit code is 0, the test success. otherwise failed
  - docker run pmc12thsuki/docker-react npm run test -- --coverage 
```

> travis use .yml instead of .yaml
> dont use .yaml if want to use travis CI

## deploy to AWS elastic beanstalk
- add deploy in .travis.yml file
```yaml=
deploy: 
  provider: elasticbeanstalk
  region: "us-east-2"
  app: "docker-react"
  env: "DockerReact-env"
  # s3 bucket name
  bucket_name: "elasticbeanstalk-us-east-2-108454134942"
  # bucket_path is a folder in s3 which name is same to our app name
  bucket_path: "docker-react"
  # only deploy when new code on master branch
  on:
    branch: master
  # aws key which store in travis Environment Variables
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
```


AWS Elastic Beanstalk
===
- will automatically scale for us
- will automatically generate an S3 bucket for us
![](https://i.imgur.com/zXTC4Jx.png)


Multi-Container application
===
## Structure
![](https://i.imgur.com/ZhZKnXz.png)

## Set up docker-compose
```yaml=
version: '3'
services: 
  postgres:
    image: 'postgres:latest'
  redis: 
    image: 'redis:latest'
  server:
    # how to build our image
    build: 
      # build with dockerfile.dev
      dockerfile: Dockerfile.dev
      # specify where is our dockerfile
      context: ./server
    volumes: 
      # do not reference this file
      - /app/node_modules
      # reference all our code in /app to ./server
      - ./server:/app
    environment: 
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - PGUSER=postgres
      - PGHOST=postgres
      - PGDATABASE=postgres
      - PGPASSWORD=postgres_password
      - PGPORT=5432
  client: 
    build:
      dockerfile:  Dockerfile.dev
      context: ./client
    volumes: 
      - /app/node_modules
      - ./client:/app
  worker:
    build:
      dockerfile:  Dockerfile.dev
      context: ./worker
    volumes: 
      - /app/node_modules
      - ./worker:/app
    
```
> evnironment variable in docker-compose
- use the syntax: **valuableName = value** will sets a variable in the container at **run time**
- use the syntax: **valuableName** will sets a variable in the container at **run time**, and the value is taken from your own computer

## How do nginx work
![](https://i.imgur.com/088xzcy.png)

## Routing nginx server
![](https://i.imgur.com/bbKTe7c.png)

- write a default.conf file to adds configuration rules in Nginx

```
# set a upstream called client
upstream client {
    # which has host:port on client:3000, host name is defined in docker-compose's services
    server client:3000;
}

# set a upstream called api
upstream api {
    # which has host:port on api:3000, host name is defined in docker-compose's services
    server api:5000;
}

# setup our nginx server
server {
    listen 80;

    # specify routing
    # for /
    location / {
        proxy_pass http://client;
    }

    # for /api
    location /api {
        # rewrite our route from /api/someurl to /someurl , then break(do not apply other rewrite rules)
        rewrite /api/(.*) /$1 break;
        proxy_pass http://api;
    }
}
```

- add nginx's dockerfile
```yaml=
FROM nginx
# overwrite the default.con in our base image with our custom default.conf
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

- add nginx into our docker-compose file
```yaml=
nginx: 
    restart: always
    build: 
      dockerfile:  Dockerfile.dev
      context: ./nginx
    ports:
      - '8000:80'
```

## Production multi-container project

### flow
- instead use elestic beanstalk to build our image, we now test and build prod imange in Travis for one time and then push the prod image to Docker hub, then Travis will tell EB a new prod image has build, then EB will pulls image from dockerhub
> Dockerhub is the central repository for many docker service, including AWS and google support to download and deploy image from dockerhub
> 
![](https://i.imgur.com/kU52WAO.png)

### make docker file for production
#### in our client side(react project)
- before when there is only a single component project, we use one ngnix for routing react project, and expose port 80 for routing
![](https://i.imgur.com/rOd3fj8.png)

- now in multi-container project, we need two ngnix, one for react itself, one for routing to requesr to client or server. in real world project, there is very likey that these two aren't both ngnix project
![](https://i.imgur.com/qvZNa36.png)

### make .travis.yml file
![](https://i.imgur.com/XTPx5Of.png)

## Multi-container deployments to AWS

### Multi-container definition files
- in the singe-container project, EB automatically build our image and run (because there is only one dockerfile)
- in multi-container project, we need to write a Dockerrun.aws.json file to tell EB how to pull our image from dockerhub and run our serveral image
- we can see the documentation about Dockerrun.aws.json in [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html), because EB delegate ECS to run container. a task definition is a file that tells us how to run **one single container**.

> [container definition](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions) is defined here