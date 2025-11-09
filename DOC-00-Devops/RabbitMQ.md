- **RabbitMQ** Message Broker software, That implement the Advanced Message Queuing Protocol (AMQP). 
- **Key Features** 
	1. *Message queuing*: Stores messages until the receiving application can process them. 
	2. *Multiple messaging patterns*: Point-to-point, publish/subscribe, request/reply. 
	3. *Reliability*: Persistence, acknowledgments, publisher confirms. 
	4. *Clustering & high availability*: for fault tolerance and scalability. 
		![[RabbitMQ-message.jpg]]
#### Core concepts
1. The difference between synchronous and a synchronous messaging is very important 
	![[syn-asyn.png]]
2. Producer (publisher): An application that sends messages to a RabbitMQ exchange.
3. Consumer (subscriber): An application that receives messages from RabbitMQ queues.
4. Queue: Buffer that stores messages until they are consumed. 
5. Exchange: Receives messages from producers and routes them to queues based on(Exchange type, Bindings, Message routing key). 
6. Exchange Types
	- Direct: Routes messages with a matching routing key
	- Fanout:  Broadcasts messages to all bound queues
	- Topic: Routes messages based on wildcard matching of routing keys

Put the following in your consideration
1. **Message Acknowledgments**: Ensure message processing completion
2. **Message Durability**: Survive broker restarts
3. **Publisher Confirms**: Ensure messages reach the broker
4. **Dead Letter Exchanges**: Handle failed messages
5. **TTL (Time-To-Live)**: Message expiration
6. **Priority Queues**: Prioritize important messages


### Hands-On Lab with Docker Containers
You can refere labs repo which contain one example 

