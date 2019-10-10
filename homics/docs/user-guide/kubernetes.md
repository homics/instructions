# Exercise 7: Kubernetes

Previously on HOMicS -> [Exercise 6: Containers](containers.md)

## Context

We saw on the previous exercises how to run our applications using containers and docker.

Maybe you noticed the `crash` button on user-activity tab. Surprisingly, this button will crash your application. It's 
our way to mock production crashes like OutOfMemory, StackOverflow, etc ... When it happens, you need to manually reboot
your container. In production, this is not acceptable.

Let's discover how Kubernetes can automatize this process.

### About Kube

// todo

## Setup 

--> present minikube

For macOS :
  $ brew cask install minikube
  Install virtual box

// todo

## Goal 

Run our market place using kube.

## At your keyboard

Checkout the branch: 
        
    git checkout exercise-7
   

1) le crah redemare le pod tout seul
2) Rolling update
3) pas d'update si la version ne passe pas les tests de vie

[Install and Set Up kubectl - Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos)

Install kubectl (via brew)

Install virtual box -> brew cask install virtualbox
For Mac, don’t forget to authorize 
To install and/or use virtualbox you may need to enable its kernel extension in:
  System Preferences → Security & Privacy → Generalbrew
then restart the installation.

Install Kube and minikube
[Install Minikube - Kubernetes](https://kubernetes.io/docs/tasks/tools/install-minikube/)
brew cask install minikube


## Docker commands :
1. mvn clean install spring-boot:repackage -Pcompile-front -pl "gateway"
2. docker build -t "handson/gateway:0.0.1-SNAPSHOT" "gateway"
3. docker run -d --rm --name "gateway" -p 8080:8080 "handson/gateway:0.0.1-SNAPSHOT"
4. docker stop gateway

## Kube :
1. minikube start
2. eval $(minikube docker-env)
2. docker build -t "handson/gateway:0.0.1-SNAPSHOT" "gateway"

We need those two lines to set the docker images for minikube.

3. kubectl apply -f deployment.yaml
4. kubectl apply -f service.yaml
5. minikube service gateway

You can verify that everything is up by using `kubectl get deploy` or `pod` or `service`.

## Get logs

First get the pod name with : kubectl get pods
then get the logs : kubectl logs $POD_NAME


## Rolling update
kubectl set image deployments/user-deployment user=handson/user-activity:0.0.2-SNAPSHOT

## DB
dans database : kubectl create -f volume.yaml
kubectl get persistentvolumes


## Trouble shooting :
If you enconter the error : invalid object doesn't have additional properties
you need to relink kubectl : brew link --overwrite kubernetes-cli




## Good to know

// todo 

## What's next ?

Come see us at our stand and let us know what you though about the Hands On.

Thank you. 
