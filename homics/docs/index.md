# Welcome to **L'Atelier des Chefs: du monolith aux micro-services**

Homics -> Hands On Micro Service

## Subject

Ingredient:

-  A fucking big monolith
-  A heterogeneous stress on the application
-  An exponential entropy

The usual recipe :

-   Take the way pasted monolith
-   Cut it equally in height parts
-   Heat it at 200 transactions per seconds
-   Remove your performant microservices from the oven

In a kitchen, this recipe seems pretty simple. In our developer world, we learn pretty fast that it's not usually the
case. When do you split a monolith? How do you extract relevant functionality? What are the difficulties we are going to
encounter? How do the microservices communicate? 

There isn't one recipe to split a monolith. This HandsOn will introduce our discovery and experience on the matter.
Join us on this journey to migrate a MarketPlace monolith into microservices.

## Presentation

**Mirakl**, the leading provider of Marketplace Solutions, started the migration of their huge application toward
microservices. Those last few years, the different teams have been facing a lot of problems. After some reflexion on 
this adventure, we realized that it's important to clarify this important and amazing process.

On one hand, we have learned at our expenses that microservices are not the solution for all the problems. On the other
hand, they are very interesting in the evolution of an application.

The goal of this HandsOn is to retrace our story through different exercises. The class will treat of the following
subjects:

- How do you split a monolith
- How do you communicate between microservices ? (HTTP, events, etc ...)
- Transaction with microservices
- Resilience in micro-service (peak load, network latency, outage ...)
   
This HandsOn is the work of three developers:

- Nicolas Favier [**@Takima**](https://www.takima.fr/)
- Eric Ndouakulu Kiaku Mbuta [**@Mirakl**](https://www.mirakl.com/)
- Benjamin Yvernault [**@Takima**](https://www.takima.fr/)

You can find all the code on [Homics Github](https://github.com/homics).

## What does microservice mean?

Microservice is an architectural style in application development where you decide to separate your application
into a set of loosely couple services. It's a difficult process with a lot of questions you need to ask yourself.

> ![question](img/question.png) But why does someone decide to start this journey?

## Why microservice?

It's a long trip to extract microservices from your monolith. You will need to migrate a lot of your architecture and 
create new pipelines for your CI but there are a lot of benefits of doing this:

- Highly maintainable and testable
- Loosely coupled
- Independently deployable
- Organized around business capabilities

What those points mean is that it makes the application easier to understand, develop, test, and become more resilient 
to architecture erosion. The management of your service can be done by a single team that does not need to know the 
application as a whole. It also enables an organization to evolve its technology stack. 
