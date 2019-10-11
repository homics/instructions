# Setup to follow the HOMicS

## **Github**

All the code for this HandsOn is hosted on github under the [homics](https://github.com/homics) project. 

You will need to install the `git` command line to checkout the code base and follow the HandsOn.

## **Java** & **Maven**

![java](img/java.png) ![maven](img/maven.png)

The back has been developed in Java using maven as software project management. We are using **java 8**. You can install
it via this [instructions](https://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html).

For **Maven**, it's [here](https://maven.apache.org/install.html).

## **Docker** & **Docker-compose**

![docker](img/docker.png) 

We are using docker starting at step 4 when you will be using kafka. Please install it.

- for Linux, on this [link](https://docs.docker.com/install/), select your OS. You can find that information via this
command `cat /etc/*release`
- for MacOs, follow this [link](https://docs.docker.com/docker-for-mac/install/)
- for Windows, follow this [link](https://docs.docker.com/docker-for-windows/install/)

For **Docker-compose**, it's [here](https://docs.docker.com/compose/install/).


## **CURL** command

You can set it up on windows via this [link](https://curl.haxx.se/download.html).

It's available on macOs and Linux by default.


## Kubernetes

To install minikube, select your OS and follow the instructions on [how to install minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/).

For MacOs users, don’t forget to authorize virtualbox to enable its kernel extension on your computer via System Preferences → Security & Privacy → General, 
then restart the installation.
