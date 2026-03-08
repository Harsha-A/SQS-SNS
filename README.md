# SQS-SNS
SQS &amp; SNS Interview Questions

SNS - You may own a Topic, Subscribers will subscribe to that topic. When you push messages to that topics, the subscribers will receive it.

https://www.youtube.com/watch?v=_gpOHGnoq_o

**Amazon SQS Questions:**

1. **What is Amazon SQS, and how does it work?**
   
   *Answer:* Amazon SQS (Simple Queue Service) is a fully managed message queuing service. It allows decoupling of the components in a distributed system, enabling them to communicate asynchronously. It works by storing messages in a queue and delivering them to consumers, helping to ensure reliability and scalability in message processing.

2. **What are the types of queues available in Amazon SQS, and how do they differ?**
   
   *Answer:* Amazon SQS offers two types of queues: Standard and FIFO (First-In-First-Out). Standard queues provide best-effort ordering and at-least-once delivery, while FIFO queues guarantee exactly-once processing and maintain message order.

3. **What are the use cases for Amazon SQS?**

   *Answer:* Amazon SQS is used for various purposes, including decoupling components, load leveling, handling transient spikes, and distributing tasks in a distributed system. It is commonly used in microservices architectures, serverless applications, and batch processing.

4. **Explain how message visibility timeout works in Amazon SQS.**

   *Answer:* The message visibility timeout is the duration during which a message remains invisible in the queue after a consumer starts processing it. If the consumer doesn't delete the message within this time, it becomes visible again for other consumers to process.

5. **How does Amazon SQS handle dead-letter queues (DLQ), and when are they useful?**

   *Answer:* Dead-letter queues are used to capture and retain messages that couldn't be processed successfully. Amazon SQS automatically moves problematic messages to a DLQ for later analysis and troubleshooting, helping identify and resolve issues in the application.

6. **What is long polling in Amazon SQS, and how does it differ from short polling?**

   *Answer:* Long polling is a feature that allows consumers to retrieve messages from SQS queues with reduced request frequency and increased efficiency. It helps to save on costs and reduce the number of empty responses. Short polling, on the other hand, involves frequent requests to check for new messages.

7. **How can you handle scaling issues in Amazon SQS consumers during high-traffic periods?**

   *Answer:* Amazon SQS consumers can be auto-scaled using AWS Lambda or EC2 instances. You can also use an Elastic Load Balancer (ELB) to distribute traffic among multiple consumers.

8. **Explain the concept of SQS message deduplication, and why is it important?**

   *Answer:* Message deduplication in SQS ensures that the same message isn't processed more than once. It is important to prevent unintended duplicate processing, especially in scenarios where idempotent processing isn't guaranteed.

9. **What is the maximum message size in Amazon SQS, and how can you handle larger messages?**

   *Answer:* The maximum message size in SQS is 256 KB. To handle larger messages, you can use Amazon S3 to store the message payload and include a reference or URL in the SQS message.

10. **What is the significance of the "ApproximateNumberOfMessages" and "ApproximateNumberOfMessagesNotVisible" attributes in an SQS queue?**

    *Answer:* "ApproximateNumberOfMessages" represents the number of messages that are available in the queue for retrieval, while "ApproximateNumberOfMessagesNotVisible" represents the number of messages currently being processed by consumers and not yet visible. These metrics help monitor the queue's status and health.

---------------------------------

Below is your content converted into a **clean FAANG-style interview Q&A study guide**. This format works well for **revision, flashcards, and mock interviews**.

---

# Amazon SQS — Advanced Interview Q&A (SDE-3 / Staff / Lead)

---

## 1. What delivery guarantee does Amazon SQS provide?

**Answer:**

Amazon SQS provides **at-least-once delivery**.

This means:

* A message will be delivered **at least once**
* **Duplicate messages may occur**

This happens because SQS stores messages **across multiple distributed servers for reliability**.

Therefore, consumers must implement **idempotent processing**.

Example architecture:

```
Order Service → SQS → Payment Processor
```

Duplicate handling logic:

```
Check if orderId already processed
If yes → ignore
Else → process payment
```

Common implementation strategies:

* Idempotency keys
* Redis cache
* DynamoDB table storing processed message IDs

---

# Throughput and Scaling

---

## 2. What are the throughput limits of SQS?

**Answer:**

### Standard Queue

Characteristics:

* Nearly **unlimited throughput**
* Messages may be **out of order**
* Supports very high parallelism

---

### FIFO Queue

Throughput limits:

Without batching:

```
300 messages per second
```

With batching:

```
3000 messages per second
```

---

## 3. How can FIFO queues scale while maintaining ordering?

**Answer:**

FIFO queues use **Message Groups**.

Each group maintains ordering internally but can run **in parallel with other groups**.

Example:

```
Group A → ordered
Group B → ordered
Group C → ordered
```

Workers can process groups **concurrently**, improving throughput.

---

# Message Lifecycle

---

## 4. What is the message retention period in SQS?

**Answer:**

Message retention determines how long messages remain in the queue if not processed.

Range:

```
1 minute → 14 days
```

Default:

```
4 days
```

Common use cases:

* Event replay
* Recovery from consumer outages
* Delayed processing

---

# Message Scheduling

---

## 5. What are Delay Queues in SQS?

**Answer:**

Delay queues postpone the delivery of messages to consumers.

Example:

```
Send message
Delay = 5 minutes
Consumer receives message after 5 minutes
```

Maximum delay:

```
15 minutes
```

Typical use cases:

* Retry mechanisms
* Cooldown timers
* Scheduled workflows

Example:

```
User Signup
      ↓
Send Welcome Email after 10 minutes
```

---

# Performance Optimization

---

## 6. What is message batching and why is it useful?

**Answer:**

Instead of sending messages individually, SQS allows **batch operations**.

Example:

```
SendMessageBatch (10 messages)
```

Benefits:

* Higher throughput
* Lower API cost
* Reduced network overhead

Batch operations include:

* SendMessageBatch
* DeleteMessageBatch
* ChangeMessageVisibilityBatch

---

# Failure Scenarios

---

## 7. What happens if visibility timeout is shorter than processing time?

**Answer:**

Scenario:

```
Worker receives message
Processing time = 2 minutes
Visibility timeout = 30 seconds
```

Result:

* Message becomes visible again
* Another worker picks it up
* **Duplicate execution occurs**

Solution:

Extend visibility timeout dynamically.

Example:

```
ChangeMessageVisibility
```

Or configure a longer visibility timeout.

---

# Reliability Mechanisms

---

## 8. What is a Redrive Policy in SQS?

**Answer:**

A Redrive Policy defines how messages move to a **Dead Letter Queue (DLQ)** after repeated failures.

Example configuration:

```
maxReceiveCount = 5
```

Processing flow:

```
Attempt 1 → fail
Attempt 2 → fail
Attempt 3 → fail
Attempt 4 → fail
Attempt 5 → fail
↓
Message moved to DLQ
```

DLQs help with:

* Debugging
* Preventing infinite retries
* Identifying poison messages

---

# Messaging Architecture

---

## 9. What is the difference between SQS and SNS?

**Answer:**

| Feature           | SQS                        | SNS                  |
| ----------------- | -------------------------- | -------------------- |
| Messaging pattern | Queue                      | Pub/Sub              |
| Consumers         | Usually one consumer group | Multiple subscribers |
| Ordering          | FIFO supported             | No ordering          |
| Use case          | Task processing            | Event broadcasting   |

Example architecture:

```
Order Service
      ↓
      SNS
   /   |   \
 SQS  SQS  Lambda
Email Analytics Billing
```

SNS distributes events to multiple systems.

---

# Messaging Platforms

---

## 10. What is the difference between SQS and Kafka?

**Answer:**

| Feature    | SQS           | Kafka                    |
| ---------- | ------------- | ------------------------ |
| Management | Fully managed | Self-managed or MSK      |
| Retention  | Up to 14 days | Configurable             |
| Replay     | Limited       | Strong replay capability |
| Ordering   | FIFO only     | Partition ordering       |
| Throughput | Medium        | Extremely high           |

Use **SQS** when:

* Simple asynchronous processing
* Serverless systems
* AWS-native architecture

Use **Kafka** when:

* Event streaming platforms
* Real-time analytics
* Event sourcing systems

---

# Exactly-Once Processing

---

## 11. How can you achieve exactly-once processing with SQS?

**Answer:**

SQS itself only guarantees **at-least-once delivery**.

Exactly-once behavior is achieved using **idempotent consumers**.

Techniques include:

### 1. Deduplication ID

```
MessageDeduplicationId
```

Used in FIFO queues.

---

### 2. Database uniqueness

Example:

```
PRIMARY KEY(order_id)
```

Duplicate messages fail the insert operation.

---

### 3. Distributed locks

Using systems like:

* Redis
* DynamoDB

---

# Serverless Architectures

---

## 12. How is SQS used with AWS Lambda?

**Answer:**

SQS commonly acts as a **buffer between services and Lambda workers**.

Architecture:

```
API Gateway
      ↓
Lambda Producer
      ↓
SQS Queue
      ↓
Lambda Consumer
      ↓
Database
```

Benefits:

* Smooth traffic spikes
* Automatic retry handling
* Serverless scalability

---

# Backpressure Handling

---

## 13. What happens if consumers are slower than producers?

**Answer:**

Queue size will increase, creating **backpressure**.

Symptoms:

* Increasing queue length
* Older messages remaining unprocessed

Solutions:

* Auto-scale workers
* Increase batch size
* Increase consumer concurrency

Key monitoring metrics:

```
ApproximateNumberOfMessagesVisible
ApproximateAgeOfOldestMessage
```

---

# Security

---

## 14. How is security handled in Amazon SQS?

**Answer:**

Security mechanisms include:

1. **IAM Policies**

Control which services can access queues.

---

2. **Queue Policies**

Define access rules for producers and consumers.

---

3. **KMS Encryption**

Encrypt messages at rest.

---

4. **VPC Endpoints**

Secure access without traversing the public internet.

Example:

```
Allow only OrderService to send messages
```

---

# Cost Optimization

---

## 15. How can SQS costs be optimized?

**Answer:**

Common strategies:

* Enable **long polling**
* Use **message batching**
* Reduce empty receives
* Use **efficient worker scaling**

These techniques reduce API calls and operational costs.

---

# System Design

---

## 16. How would you design a scalable email notification system using SQS?

**Answer:**

Architecture:

```
User Service
      ↓
      SQS Queue
      ↓
Email Worker Fleet
      ↓
SMTP Provider
```

Advantages:

* Handles traffic spikes
* Retry on failure
* Decouples services

---

# Production Failure Modes

---

## 17. What are poison messages and how are they handled?

**Answer:**

Poison messages are messages that **always fail processing**.

Handling approach:

```
Retry limited times
↓
Move to DLQ
```

Developers can inspect DLQ to debug failures.

---

## 18. What is a message storm and how can it be handled?

**Answer:**

A message storm occurs when producers send **extremely high message volume**.

Solutions:

* Rate limiting producers
* Auto-scaling workers
* Backpressure mechanisms

---

## 19. What happens when a queue backlog occurs?

**Answer:**

Queue backlog occurs when consumers cannot keep up.

Solutions:

* Increase worker fleet
* Use batch processing
* Increase concurrency

---

# Advanced Architecture

---

## 20. What is the SNS + SQS fan-out pattern?

**Answer:**

SNS can broadcast events to multiple SQS queues.

Architecture:

```
Producer
   ↓
   SNS
 /  |  \
SQS SQS SQS
 |   |   |
Worker Worker Worker
```

Each system processes the event independently.

This is widely used in **large event-driven systems**.

---

# Advanced Interview Question

---

## 21. How would you guarantee ordered processing while maintaining high throughput?

**Answer:**

Use **FIFO queues with Message Groups**.

Example:

```
MessageGroupId = userId
```

Properties:

* Ordering guaranteed within each user
* Parallel processing across users
* High overall throughput

---

# Important Adjacent Topics for FAANG Interviews

You should also understand:

* SQS vs EventBridge
* SQS vs Kinesis
* Lambda + SQS scaling
* Distributed retries
* Idempotency patterns
* Saga pattern
* Event-driven architecture


---------------------------------
**Amazon SNS Questions:**

1. **What is Amazon SNS, and how does it differ from SQS?**
   
   *Answer:* Amazon SNS (Simple Notification Service) is a fully managed publish-subscribe messaging service. It differs from SQS in that it is designed for pub-sub messaging, allowing messages to be sent to multiple subscribers simultaneously, while SQS is a message queuing service for point-to-point communication.

2. **What are the key components of Amazon SNS?**
   
   *Answer:* Amazon SNS consists of topics, subscribers, and publishers. Topics are message categories, subscribers receive and consume messages, and publishers send messages to topics.

3. **Explain how message delivery policies work in Amazon SNS.**

   *Answer:* Message delivery policies in Amazon SNS allow you to control message delivery to different types of endpoints and devices. You can configure policies based on message attributes and endpoint types (e.g., SMS, email, HTTP).

4. **What are the supported protocols for message delivery in Amazon SNS?**

   *Answer:* Amazon SNS supports various protocols, including HTTP/HTTPS, SMS, email, Lambda, SQS, application endpoints (e.g., mobile devices), and more.

5. **What is the purpose of subscribing endpoints to an Amazon SNS topic?**

   *Answer:* Subscribing endpoints to an SNS topic enables the endpoints to receive messages published to that topic. Subscribers can be AWS resources (e.g., Lambda functions, SQS queues) or external endpoints (e.g., email addresses or SMS numbers).

6. **Explain how message filtering is implemented in Amazon SNS.**

   *Answer:* Message filtering in SNS allows you to control which subscribers receive specific messages based on message attributes. You can define filter policies for individual subscribers to customize message routing.

7. **How does Amazon SNS handle message retries and retries to failed endpoints?**

   *Answer:* Amazon SNS supports message retries to prevent message loss due to transient issues. If a message delivery fails to an endpoint, SNS will retry the delivery several times. For endpoints with permanent failures, you can configure a dead-letter queue (DLQ) to capture failed messages.

8. **What is the difference between fanout and point-to-point messaging in Amazon SNS?**

   *Answer:* Fanout messaging involves sending a message to multiple subscribers simultaneously, while point-to-point messaging sends messages to a single subscriber. SNS excels at fanout messaging, whereas SQS is suitable for point-to-point messaging.

9. **How can you secure your Amazon SNS topics and messages?**

   *Answer:* You can secure SNS topics by implementing access policies and using AWS Identity and Access Management (IAM) roles. Additionally, you can encrypt messages at rest and in transit.

10. **What is the AWS Lambda destination for Amazon SNS, and how can it be used?**

    *Answer:* The AWS Lambda destination for Amazon SNS allows you to invoke Lambda functions in response to SNS messages. It is useful for processing and acting upon notifications received via SNS.
    
12. **What was your batch size in SQS?**
13. **What was your DLQ Size in your project?** 
