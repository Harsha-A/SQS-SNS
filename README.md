# SQS-SNS
SQS &amp; SNS Interview Questions

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
