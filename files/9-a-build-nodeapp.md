# Build your Node image

# Overview

To complete this tutorial, you need the following:

- Node.js version 12.18 or later. [Download Node.js](https://nodejs.org/en/)
- Docker running locally: Follow the instructions to [download and install Docker](https://docs.docker.com/desktop/).
- An IDE or a text editor to edit files. We recommend using Visual Studio Code.
## Installing Node on Linux 

```Console
Centos
$ yum install node npm -y
Ubuntu
$ apt install npm node -y
```

## Sample application

```console
 $ cd [path to your node-docker directory]
 $ npm init -y
 $ npm install ronin-server ronin-mocks
 $ touch server.js
```
update server.js file 
```console
const ronin = require('ronin-server')
const mocks = require('ronin-mocks')

const server = ronin.server()

server.use('/', mocks.server(server.Router(), false, true))
server.start()
```

The mocking server is called Ronin.js and will listen on port 8000 by default. You can make POST requests to the root (/) endpoint and any JSON structure you send to the server will be saved in memory. You can also send GET requests to the same endpoint and receive an array of JSON objects that you have previously POSTed.

## Test the application
Let’s start our application and make sure it’s running properly. Open your terminal and navigate to your working directory you created.


To test that the application is working properly, we’ll first POST some JSON to the API and then make a GET request to see that the data has been saved. Open a new terminal and run the following curl commands:

```console
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing" }'

$ curl http://localhost:8000/test
```
Switch back to the terminal where our server is running. You should now see the following requests in the server logs.

We have successfully ececuted the app locally. Now lets build a Docker Image

# Create a Dockerfile for Node.js

Create a Docker file and add below content 
```

FROM node:12.18.1

ENV NODE_ENV=production

WORKDIR /app

COPY ["package.json", "package-lock.json*", "./"]

RUN npm install --production

COPY . .

CMD [ "node", "server.js" ]
```

## Create a .dockerignore file

Create a **.dockerignore** file and add **node_modules**

## Build image

```console
$  docker build --tag node-docker .

# Once the build is successfull you can list the images 

$ docker images 
```

# Tag images

```console
$ docker tag node-docker:latest node-docker:v1.0.0
```

# Run your image as a container

Start the container and expose port 8000 to port 8000 on the host.

```console
$ docker run --publish 8000:8000 node-docker
```
Now let’s rerun the curl command from above. Remember to open a new terminal.

```console
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'
```
## Run in detached mode

This is great so far, but our sample application is a web server and we should not have to have our terminal connected to the container. Docker can run your container in detached mode or in the background. To do this, we can use the --detach or -d for short

```console
$ docker run -d -p 8000:8000 node-docker

# test
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'

```
# Use containers for development

 we’ll walk through setting up a local development environment for the application we built in the previous steps

### Local database and containers

```console
$ docker volume create mongodb
$ docker volume create mongodb_config
```

 The network is called a user-defined bridge network and gives us a nice DNS lookup service which we can use when creating our connection string.

 ```console
$ docker network create mongodb
```
Now we can run MongoDB in a container and attach to the volumes and network we created above.

```console
$ docker run -it --rm -d -v mongodb:/data/db \
  -v mongodb_config:/data/configdb -p 27017:27017 \
  --network mongodb \
  --name mongodb \
  mongo
```
Okay, now that we have a running MongoDB, let’s update server.js to use MongoDB and not an in-memory data store

```node
const ronin     = require( 'ronin-server' )
const mocks     = require( 'ronin-mocks' )
const database  = require( 'ronin-database' )
const server = ronin.server()

database.connect( process.env.CONNECTIONSTRING )
server.use( '/', mocks.server( server.Router(), false, false ) )
server.start()
```
We’ve added the ronin-database module and we updated the code to connect to the database and set the in-memory flag to false. We now need to rebuild our image so it contains our changes.

First let’s add the ronin-database module to our application using npm.

```console
$ npm install ronin-database
```
Now we can build our image

```console
$ docker build --tag node-docker .
```


Now, let’s run our container. But this time we’ll need to set the CONNECTIONSTRING environment variable so our application knows what connection string to use to access the database. We’ll do this right in the docker run command.

```console
$ docker run \
  -it --rm -d \
  --network mongodb \
  --name rest-server \
  -p 8000:8000 \
  -e CONNECTIONSTRING=mongodb://mongodb:27017/notes \
  node-docker
```
The notes at the end of the connection string is the desired name for our database.

Let’s test that our application is connected to the database and is able to add a note.

```console
$ curl --request POST \
  --url http://localhost:8000/notes \
  --header 'content-type: application/json' \
  --data '{"name": "this is a note", "text": "this is a note that I wanted to take while I was working on writing a blog post.", "owner": "peter"}'
```

## Use Compose to develop locally

we’ll create a Compose file to start our node-docker and the MongoDB with one command. We’ll also set up the Compose file to start the node-docker in debug mode so that we can connect a debugger to the running node process.

Open the notes-service in your IDE or text editor and create a new file named **_docker-compose.dev.yml_**. Copy and paste the below commands into the file.

```yaml
version: '3.8'

services:
 notes:
  build:
   context: .
  ports:
   - 8000:8000
   - 9229:9229
  environment:
   - SERVER_PORT=8000
   - CONNECTIONSTRING=mongodb://mongo:27017/notes
  volumes:
   - ./:/app
  command: npm run debug

 mongo:
  image: mongo:4.2.8
  ports:
   - 27017:27017
  volumes:
   - mongodb:/data/db
   - mongodb_config:/data/configdb
volumes:
 mongodb:
 mongodb_config:
```

This Compose file is super convenient as we do not have to type all the parameters to pass to the docker run command. We can declaratively do that in the Compose file.

We are exposing port 9229 so that we can attach a debugger. With volumes, we are also mapping our local source code into the running container so that we can make changes in our text editor and have those changes picked up in the container.

To start our application in debug mode, we need to add a line to our **_package.json_** file to tell npm how to start our application in debug mode.

As you can see, we are going to use nodemon. Nodemon starts our server in debug mode and also watches for files that have changed, and restarts our server. Let’s run the following command in a terminal to install nodemon into our project directory.

```console
$ npm install nodeman
```

Let’s start our application and confirm that it is running properly.
```console
 $ docker-compose -f docker-compose.dev.yml up --build
```

# Run your Tests using Node.js and Mocha frameworks

## Introduction

Testing is an essential part of modern software development. Testing can mean a lot of things to different development teams. There are unit tests, integration tests and end-to-end testing. In this guide we take a look at running your unit tests in Docker.

## Create a test
Let’s define a Mocha test in a ./test directory within our application.

```console
$ mkdir -p test
```
Save the following code in ./test/test.js.
```js
var assert = require('assert');
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal([1, 2, 3].indexOf(4), -1);
    });
  });
});
```
## Running locally and testing the application

```console
$ docker-compose -f docker-compose.dev.yml up --build
```
Now let’s test our application by POSTing a JSON payload and then make an HTTP GET request to make sure our JSON was saved correctly.

```json
$ curl --request POST \
  --url http://localhost:8000/test \
  --header 'content-type: application/json' \
  --data '{"msg": "testing"}'
```
Now, perform a GET request to the same endpoint to make sure our JSON payload was saved and retrieved correctly.

```console
curl http://localhost:8000/test
```

