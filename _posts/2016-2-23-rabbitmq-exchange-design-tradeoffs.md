---
layout: post
title: RabbitMQ Exchange & Queue Design Trade-off
tags:
  - rabbitmq
---

In [previous post](/2016/02/22/understanding-rabbitmq-exchange-and-queue/), I mentioned the [discussion](http://stackoverflow.com/questions/32220312/rabbitmq-amqp-best-practise-queue-topic-design-in-a-microservice-architecture) on StackOverflow regarding designing exchanges. Usually, when people ask about best practice of designingexchanges, you will get some useful suggestions, but finally you will be told that the final solution always "depends on your system needs". Fair enough & safe enough answer, but not really helpful enough. So I want to extend this discussion a little bit here with more detailed backgrounds.

Before discussing any solutions, I'm trying to suggest some rules first. No matter which solutions to choose, these rules should be correct in most cases and should be followed if there are no compelling reasons.

**Rule 1**
Each queue should only represent one type of jobs. Do not mix different type of messages in one queue. And when this rule is followed, we could clearly name a queue with the job represented by it.

**Rule 2**
Avoid messgae re-dispatching on a queue. If you find that your subscriber is trying to re-dispatch any messages to other places without real processing, there might be something wrong. Routing or Dispatching is the responsibility of exchanges rather than queues.

**Rule 3**
When not using the global default exchange, publishers should know nothing about queues. One of the essences of the AMQP design is the responsibility separation of exchange & queue, so that publishers don't need to care about message comsuption, and comcumers don't need tocare about where comes the messages at all. So when designing exchanges, the concepts only related to publishing, such as routing keys & message headers, should not has any knowledge of the queues they will finally been forwarded to.

Now let's focus on the examples. The scenario is that you want to design exchanges and queues for "user" related write operations. The write events will be triggered in one app and these messages are to be consumed by some other apps.

``` text
| object | event   |
|------------------|
| user   | created |
| user   | updated |
| user   | deleted |
```

The first question people usually ask is for different events of one object (the "user" object in this example), should we use one exchange for publishing all the 3 events, or use 3 separated exchanges for each event? Or, in short, one exchange, or many?

Before answering this question. I actually want to ask another question: do we really even need a custom exchange for this case?

Different types of object events are so natual to match different types of messages to be published, but it is not really necessary sometimes. What if we abstract all the 3 types of events as a "write" event, whose sub-types are created, updated and deleted?

``` text
| object | event   | sub-type |
|-----------------------------|
| user   | write   | created  |
| user   | write   | updated  |
| user   | write   | deleted  |
```

**Solution 1**
The simplest solution to support this is we could only design a "user_write" queue, and publish all user write event messages to this queue directly via the global default exchange. When publishing to a queue directly, the biggest limitation it assumes that only one app subscribes to this type of messages. Multiple instances of this app subscribing to this queue is fine. 

``` text
| queue      | app  |
|-------------------|
| user.write | app1 |
```

**Solution 2**
The simplest solution could not work when there is a second app (having different processing logic) want to subscribe to each message published to the queue. When there are multiple apps subscribing, we at least need one fanout type exchange with bindings to multiple queues. So that messages are published to the excahnge, and the exchange duplicates the messages to each of the queues. Each queue represents the processing job of each different app.

```text
| queue           | subscriber  |
|-------------------------------|
| user.write.app1 | app1        |
| user.write.app2 | app2        |

| exchange   | type   | binding_queue   |
|---------------------------------------|
| user.write | fanout | user.write.app1 |
| user.write | fanout | user.write.app2 |
```

This second solution works fine if each subscriber does care about and want to handle all the sub-types of "user.write" events or at least to expose all these sub-type events to each subscribers is not a problem. For instance, if the subscriber app is for simply keeping the transction log; or although the subscriber handles only user.created, it is ok to let it know when user.updated or user.deleted happens. It becomes less elegant when some subscribers are from external of your group, and you only want to notify them about some specific sub-type events. For instance, if app2 only wants to handle user.created and it should not have the knowledge of user.updated or user.deleted.

**Solution 3**
To solve the issue above, we have to extract "user.created" concept from "user.write". The "topic" type of exchange could help. When publishing the messages, let's user user.created/user.updated/user.deleted as routing keys, so that we could set the binding key of "user.write.app1" queue be "user.*" and binding key of "user.created.app2" queue be "user.created".

```text
| queue             | subscriber  |
|---------------------------------|
| user.write.app1   | app1        |
| user.created.app2 | app2        |

| exchange   | type  | binding_queue     | binding_key  |
|-------------------------------------------------------|
| user.write | topic | user.write.app1   | user.*       |
| user.write | topic | user.created.app2 | user.created |
```

**Solution 4**
The "topic" exchange type is more flexible in case potentially there will be more event types. But if you clearly know the exact number of events, you could also use the "direct" exchange type instead for better performance.

```text
| queue             | subscriber  |
|---------------------------------|
| user.write.app1   | app1        |
| user.created.app2 | app2        |

| exchange   | type   | binding_queue    | binding_key   |
|--------------------------------------------------------|
| user.write | direct | user.write.app1   | user.created |
| user.write | direct | user.write.app1   | user.updated |
| user.write | direct | user.write.app1   | user.deleted |
| user.write | direct | user.created.app2 | user.created |
```

