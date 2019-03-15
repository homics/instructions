# Instructions
Instructions to split the monolith in micro-services 

# Exercice 0 : The Monolith

### What does it do

### How does it do it
// todo shema d'archy, techno

### Why micro service ?
//  - Separation of concern


# Exercise 1 : User Activity

### Context
In our marketplace we can track user activity, when they logged in or out and when. This functionality is already present in the monolith codebase. 

In `WebSecurityConfig` we have configured our monolith so 'CustomizeAuthenticationSuccessHandler' and 'CustomizedLogoutSuccessHandler' to catch each login and logout. They call `UserActivityService` which save the event in database through `UserActivityRepository`.
The front fetch those events for display through an internal API in `UserActivityInternalController`.

### Goal 
We are going to create a new service in charge of users activities.
It will store and display all users activities in a dedicated page.
The monolith will call this micro-service via API to notify a login or logout.

### Steps 
- Fetch the user-activity repository.
- Configure the application.yaml so the micro-service has a Database, and run on 9001
- Complete the `UserActivityApiController` and `UserActivityApiController` to handle post querry on "/user/api/activity"
- On the monolith, remove all uer-activity usage. (we took care of the front for you)
- On the monolith, call the micro-service on login and logout
   `restTemplate.postForEntity(USER_ACTIVITY_URL, userActivity, Void.class);`
   
# Exercise 2 : Gateway

### Context
What happens if you connect directly to you userActivity micro-service ?
-> The loggin is skiped and the data is accessible !
We could duplicate all the security code in the new micro sevice. But image if we have 20 micro-servicies or the mess it will be if we need to update the security.

### Goal 
Create a new micro service responsible for all authentifiaction and redirection. Such a micro service is call a Gateway.

### Steps 
- Fetch the gateway repository.
- Update the application.yaml so the gateway run on 8080 and zuul is setup
  see (zuul documentation)[https://cloud.spring.io/spring-cloud-netflix/multi/multi__router_and_filter_zuul.html]
- Update the WebSecurityConfig
- On login and logout call the user-microservice
- remove all security and user-activity code from monolith
- Add the logged user to the headers so all micro services will be able to retive the connected user.


# Exercise 3 : Stats

### Context
The stats are long to compte and doesn't impact the payment workflow. We are going to extract it in a micro service.
But this time we xant to be sure that the stats are corrects and no data are lost.
For exemple what happens if the stat micro-service is down ?
 -> All data sent durring the down time is lost.
 
A simple way to fix this issue is to work with acknowledgment. Let's do it.

### Goal 
The monolith will save in a new table all orders stats thats need to be send. A schedule task fetch this table and send its content to the stat micro-service. If the data is sent (200), we remove it from the table, else it will be resent at the next iteration.

### Steps 
- Fetch the stat repository.
- In the monolith create a `StatsTask`. The task crawll the `OrderStat` table and send the stats to the microservice using : "http://localhost:9002/stats/api/orders". On success (200) the orderStat is remove from the table.

### Result 
Stop your stat micro-service, make some orders. Start your stat micro-service, all the stats are retrived.

# Exercise 4 : Stats with Kafka

### Context
The previous solution has a considarable drawback, we have to add logic in the monolith and it's not really scallable.

### Goal 
Replace this manual aproch by using kafka. All the kafka config is already done for you. it's contained in the module `common-messaging`. 

To send a message with kafka :

```
  private KafkaTemplate<String, OrderPayedMessage> kafkaTemplate;

  Message<OrderPayedMessage> message = MessageBuilder
                .withPayload(new OrderPayedMessage(1,1,"user"))
                .setHeader(KafkaHeaders.TOPIC, TOPIC_STATS)
                .build();

        kafkaTemplate.send(message);
```

To retrive a message with kafka :

```
   @KafkaListener(topics = TOPIC_STATS, groupId = GROUP_ID, containerFactory = "statsMessageFactory")
   public void onImpactStockMessage(@Payload OrderPayedMessage impactStockMessage) {
      
    }
```

### Steps 
- Checkout on exo4-kafka  on the stat repository.
- Listen to kafka and retrive OrderPayedMessages.
- On the monolith send OrderPayedMessages and remove `StatsTask` and `OrderStat` table.

