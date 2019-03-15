# Exercise 5 : Stock

Previously on HOMicS -> [Exercise 4: Stats with Kafka](../user-guide/kafka.md)

## Context

Often when splitting microservice, the question of transactions arises.
Let's try to discover why through the extraction of the **stock** microservice.

### About the stock micro-service

Stock modifications are often spread around a marketplace application. It could be modified from cart, refunds, or inventory.
The stock microservice will store and update the stock for each article. 

### The existing

Before the migration when we pay an order, we were executing the following steps:

1. Open transaction

2. Check the stock

3. Impact the stock

4. Update the order status

5. Close the transaction

### Extract it into a microservice

If we extract the stock service in a microservice like we did before, the steps become :

1. Open transaction

2. Ask the micro service to impact the stock

3. Wait for the response

4. Update the order status

5. Close the transaction

With this plan, you can see in step 3 that the monolith is waiting for the microservice response.

> ![warning](../img/warning.png) It's pretty bad! 

Imagine that the call to impact stock times out... 

Your payment method is going to wait the whole time and so is your user.

> ![tip](../img/success.png) We need to change to asynchronous.

### Going async


1. Open transaction

2. Ask the micro service to impact the stock

3. Close the transaction

Then, we listen to the microservice event.

1. The microservice call the monolith when the stock is impacted

2. Open transaction

3. Update the order status

4. Close the transaction


> ![question](../img/question.png) What happens if the result of the microservice is "not enough stock" ?

We save the order as cancelled.

## Goal 

Extract the stock micro service using kafka.

The workflow becomes:

1. An ImpactStockMessage is send to kafka

2. The stock micro-service verifies and then impacts the article stock

3. A StockAcknowledgmentMessage is send with a status succeed or not

4. The monolith consumes the message and change the order status accordingly

![stock](../img/stock.png)

## At your keyboard

1. Remove the stock column in the monolith

2. Implement the payment workflow previously describe in `OrderService`.
    (Topics and messages are already setup in the common-messaging lib)

3. Implement the payment workflow on the micro service side in `StockController`


## Good to know

When working with kafka, we can set the configuration to have either:

1. All messages received but with possible duplicates.

2. No duplicate but you can miss some messages.

We are working with the first choice. It constrains us to have idempotent messages.

> ![info](../img/info.png) Idempotent means that an action always gives the same result even if you played several times.

In our case, the same message can be read multiple times and will give the same result. For example, in the stats microservice,
the second message only updates the data since the orderId is unique.

It explains why we keep the `table StockOperation`.
  
## What's next ?

Come see us at our stand and let us know what you though about the Hands On.

Please your feedback means a lot to us. If you have 5 mins to spare, could you fill this
[survey](https://docs.google.com/forms/d/1VAbfBPXvYej24ciUkATdY9dSuQVxMxfBG3psfe4mQoo/edit)

Thank you. 
