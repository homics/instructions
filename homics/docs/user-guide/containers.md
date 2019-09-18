# Exercise 6 : Containers

Previously on HOMicS -> [Exercise 5: Stock](../user-guide/stock.md)

## Context

We saw on the previous exercises how to build microservices, let's see how to deploy this infrastructure.

### About the Containers

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

The container allow us to have independent and decoupled environment for each of our microservices.

## Goal 

Run our market place using docker.

## At your keyboard

Checkout the branch: 
        
    git checkout exercise-6
   
A docker file is composed  : 

    FROM openjdk:8u222-jre
    
    COPY target/gateway-0.0.1-SNAPSHOT.jar /gateway-0.0.1-SNAPSHOT.jar
    
    EXPOSE 8080
    
    CMD ["java", "-jar", "/gateway-0.0.1-SNAPSHOT.jar"]


## Todo's

// remove we will do it for them
build the app : mvn clean install -D skip.tests

Create the docker images.
docker image build -t homics/gateway:0.0.1 .

Run the previously created image
docker run homics/gateway:0.0.1

To automate all this run complete the lazy.sh file to stop and run all the images

Execute the lazy.sh script and verify the marketplace is running




## Verify your work

When you run yours applications, you see all the pages.
There is a new btn on User activity called "Crash" when you click on it an NPE is raised. You can see the the docker is then stoped and you have to restart your application.


## Good to know

// todo 



## What's next ?

We are gonna see how to restart container automatically.

// kube
