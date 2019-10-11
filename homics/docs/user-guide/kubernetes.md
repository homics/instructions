# Exercise 7: Kubernetes

Previously on HOMicS -> [Exercise 6: Containers](containers.md)

## Context

We saw on the previous exercises how to run our applications using containers and docker.

Maybe you noticed the `Crash` button on user-activity tab. Surprisingly, this button will crash your application. It's 
our way to mock production crashes like OutOfMemory, StackOverflow, hardware failures, etc ... When it happens, you need
to manually reboot your container. In production, this is not acceptable.

Let's discover how Kubernetes can automatize this process.

### About Kubernetes

![kubernetes](../img/kubernetes.svg)

Kubernetes is an open source container orchestration engine for automating deployment, scaling, and management of containerized
applications. Kubernetes provides you with a framework to run distributed systems resiliently. We are not going to explain every
concept behind it but we will introduce some key notions before starting the exercise. [Kubernetes documentation](https://kubernetes.io/docs/concepts/)
is very well designed and will probably answer most of your questions.

First of all, a `pod` is the most basic unit in kubernetes. It can encapsulate one or more containers that are tightly coupled and that share
resources. In our case, one pod corresponds to one container, meaning one single instance of an application. We can have several pods of the same application
that are called `replicas`. There are the key of resilience and availability. 

Kube manages on which machines the pod and its replicas are running. Those machines (VM or physical) are named `Nodes`.

Kube provides two other objects, `Deployment` and `Service`. Deployment describes a desire state of a cluster like the number
of replicas. A service defines a logical set of Pods and a policy by which to access them.

## Minikube

Minikube is a tool that runs a single-node Kubernetes cluster in a virtual machine on your personal computer.

To set it up, go back to [Setup](../setup.md).

CheatSheet :

```
# Minikube start (or stop)
minikube start

# Get the state of your pods
kubectl get pod

# Get the state of your deployments
kubectl get deploy

# Get the state of your services
kubectl get service

# Apply a deployment or a service
kubectl apply -f file.yml

# Delete a deployment
kubectl delete deploy {deployment-name}

# Delete a service
kubectl delete service {service-name}

# Describe an object
kubectl describe service {service-name}

# Get the logs
kubectl logs {pod-name}

# Minikube launch the service page
minikube service {service-name}
```

## Goal 

Automatize the deployment of our market place using Kubernetes.

## At your keyboard

Checkout the branch: 
        
    git checkout exercise-7

All the docker's images are available on docker hub under the following names:

| **Services** | **Docker image name** |
|--------------|-----------------------|
| gateway      | homics/gateway:1.0.0  |
| monolith     | homics/monolith:1.0.0 |
| user         | homics/user-activity:1.0.0 |
| stats        | homics/stats:1.0.0    |
| stock        | homics/stock:1.0.0    |

### Gateway

1 - Create a deployment.yml file for gateway and run `kubectl apply -f deployment.yml`

![info](../img/info.png) You can find everything you need on Kubernetes documentation [Writing a deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment).

If you run `kubectl get deploy` and `kubectl get pod`, you should get something similar to:

```
> kubectl get deploy
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
gateway-deployment     1/1     1            1           30s

> kubectl get pod
NAME                                    READY   STATUS    RESTARTS   AGE
gateway-deployment-7895c9c479-bdztx     1/1     Running   0          30s
```

Above, we see that you have one deployment running, that is up to date with one pod available.
This pod is in the state Ready and Running with no restart. The pod has been created 30 seconds ago.

Unfortunately, at this step, you can't access your gateway from your browser. To access your pod from the outside, we need to
define a service.

2 - Create a service.yml file for gateway and run `kubectl apply -f service.yml`

![info](../img/info.png) You can find everything you need on Kubernetes documentation [Writing a service](https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service).

A service is defined by its type. By default, it is `ClusterIp` which means your pod is accessible only in your cluster.
In some parts of our application (the front), we might need to expose our service to the outside world. For this case, you
can specify a different type such as `NodePort` or `LoadBalancer`. In our case, we will use NodePort.

```
spec:
  type: NodePort
  ports:
    ...
```

Run `kubectl get service` to see your newly created services

Run `minikube service gateway` to access your gateway. 

### User and Database

1 - Create a deployment and service for User and the database. 

2 - Crash you application by accessing User-Activity tab and clicking on the button. 

You realize that the page is not accessible anymore and when you run `kubectl get pod`, you can see:

```
NAME                                    READY   STATUS    RESTARTS   AGE
gateway-deployment-7895c9c479-bdztx     1/1     Running   0          5m31s
homics-db-deployment-7bbf5fc44b-zggs4   1/1     Running   0          3m15s
user-deployment-7889589595-pzkv5        0/1     Running   1          3m10s
```

Your user-pod is running but the number ready is 0/1. Your pod isn't accessible and you can see that the number of restarts
increased to one. You pod is restarting automatically. In few seconds, you should see again your activity's page. 

### Rolling Update for User

Your application keeps evolving through time and you often need to update it. Unfortunately, you don't want to call your
client and tell him that you need to shutdown the app for few seconds/minutes.

Kube manages rolling updates very efficiently. It creates a new pod, install the new version, and then move the traffic
from the old pod to the new one, to finally kill the old one. No downtime, great. Let's roll user's app with its version 2.0.0.

1 - Open the user-activity tab and click on `version` button. It prints the version of your app every second. 

2 - Run `kubectl set image deployments/user-deployment user=homics/user-activity:2.0.0`

And the version should change from 1.0.0 to 2.0.0 on the user-activity page but you realize that there are some lines
`Error calling the server`.

![question](../img/question.png) What happens ?

Kube does not know when a pod is actually ready. The traffic is directed to the new pod even so the spring app is still
starting. Kube defines the notion of `readiness` in a deployment file. You can specify when an app is ready and Kube 
won't allow access on the pod before the condition is met. 

![info](../img/info.png) You can check [How to define readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#define-readiness-probes).

In our case, our pod is ready when we can access `/user/internal/version` on port 9001 of our application.

3 - Set back the image to 1.0.0 and see that you don't have any error.

### Replicas and crashes

While crashing your application, you realize that we had some downtime. When we introduce the term of replica, we define
them as the key for resilience and availability. Right now, User pod isn't available if one pod crashes. We are going to
increase the number of replica to 2.

1 - Increase the number of replica to 2 in your deployment.yml

2 - Crash your application and reload the page

You realize that the service is always available. 

### Bonus: Deploy our market place

Since you finish all the different steps of this last exercise, you can keep going by deploying all our microservices into
minikube. Start with monolith and kafka. Add stats and stock.

## Trouble shooting :
If you encounter the error : invalid object doesn't have additional properties
you need to relink kubectl : brew link --overwrite kubernetes-cli

## Good to know

// todo 

## What's next ?

Come see us after hand and let us know what you though about the Hands On.

Thank you. 
