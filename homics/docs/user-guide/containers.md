# Exercise 6 : Containers

Previously on HOMicS -> [Exercise 5: Stock](stock.md)

## Context

We split our monolith into several microservices. Now, it's time to deploy this infrastructure.

### About the Containers

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly
and reliably from one computing environment to another.

A Docker container image is a lightweight, standalone, executable package of software that includes everything
needed to run an application: code, runtime, system tools, system libraries and settings.

The container allow us to have independent and decoupled environment for each of our microservices.

## Goal 

Deploy our market place via docker.

## Reminder

| **services** | **port** |
|----------|------|
| gateway | 8080 |
| monolith | 8090 |
| user | 9001 |
| stats | 9002 |
| stock | 9003 |


## At your keyboard

Checkout the branch: 
        
    git checkout exercise-6

### Gateway's container

1 - Create your Dockerfile to build the docker image for gateway.

![info](../img/info.png) You can find some information on [docker website : step-2-create-a-dockerfile](https://docs.docker.com/compose/gettingstarted/#step-2-create-a-dockerfile).

![tip](../img/success.png) For each microservice, you can use the following base image to deploy the app : `openjdk:8u222-jre`.

2 - Generate the docker image described by your Dockerfile

```bash
docker build -t {image-name} {dockerfile-folder}

# E.g:

docker build -t "gateway-image" gateway
```

3 - Run a new container from your image

```bash
docker run -d --name {container-name} -p {external-port}:{container-port} {image-name}

# E.g:

docker run -d --name gateway-container -p 9000:8080 gateway-image

```

In this example, you will launch a new container for your gateway. The port 9000 of your computer is
redirected to the port 8080 on your container. 

Check the docker's cheatsheet below:

```
# Available docker's images
docker images

# Running container's listing
docker ps

# Remove a docker image
docker rmi {image-id}

# Remove a docker container
docker rm {container-id}

# Stop a docker container
docker stop {container-id/container-name}

# Kill a docker container
docker kill {container-id/container-name}
```

4 - Verify on [localhost:8080](http://localhost:8080) that your gateway is running

### User-activity and Database

Unlike gateway, User-activity required a database. Previously, it was an in-memory database
h2. In real world, we need a persistent database. We already updated all our microservices to
use a database on port 1521.

> ![info](../img/info.png) Usually, each service comes with its database. It's one of the advantages of microservices.
Each service can choose a database according to its needs. It allows your team more flexibility for the backup/deployment/upgrade.
For simplicity, we are deploying a unique database.

1 - Create the database dockerfile 

2 - Create the user-activity dockerfile

3 - Generate the image and run the containers

Go on [localhost:8080](http://localhost:8080) to access your gateway. 
Go on [localhost:9001/user-activity](http://localhost:9001/user-activity) to access user-activity page.
Log on gateway and navigate to user-activity tab. ![warning](../img/warning.png) 404 Not Found. The page isn't accessible and
on user-activity, you don't see any new login.

![question](../img/question.png) Why is that ?

By default, docker's container runs on different networks so they cannot communicate directly with each other.

For communication among containers, you can either manage routing at the OS level, or you can use an overlay network.
We are going to use docker-compose to achieve this purpose.

### Docker-compose

Compose is a tool for defining and running multi-container Docker applications. It deploys several containers within
the same network described in a YAML file. In it, we list each application by its name and define how to build its
container.

For example, in this compose, we have one application named `webapp`:

```
version: '3'
services:
  webapp:
    image: my-webapp-image
    ports:
      - "8080:8080"
```

1 - Complete the docker-compose.yml

Remember when we were routing our applications via Zuul in the gateway, we set the path for each of our microservices like
`http://localhost:9001/user`. In our case, `localhost` becomes `user`. 

2 - Update the `gateway/application.yml`, build the jar: `./buildJars gateway`, and finally rebuild the docker image.

3 - Run the docker-compose: `docker-compose up`

4 - Navigate on [localhost:8080](http://localhost:8080), log in, and verify that user-activity is accessible with the recent activity.

### Bonus:

You can complete the docker-compose to deploy all microservices and kafka with a single command line.

![warning](../img/warning.png) Be aware that this operation is CPU and RAM consuming to run everything.
Some of you might encounter some difficulties.

## Good to know

In a docker-compose, some applications might depend on others. For example, monolith depends on kafka and the database. 
You can specify that information by adding the keyword `depends_on:`:

```
version: '3'
services:
  webapp:
    image: my-webapp-image
    ports:
      - "8080:8080"
    depends_on:
      - database
      - kafka
```

## What's next ? [Exercise 7: Kubernetes](kubernetes.md)

We are gonna see how to orchestrate everything automatically with Kubernetes.
