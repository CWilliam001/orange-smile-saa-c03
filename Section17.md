Messaging
Synchronous communications (application to application)
    Synchronous between applications can be problematic if there are sudden spikes of traffic
    What if you need to suddenly encode 1000 videos but usually it's 10?
    In that case, its better to decouple your applications
        SQS: Queue model
        SNS: PUB/SUB model
        Kinesis: Real-time streaming model
Asynchronous / Event based (application to queue to application)

Messages
Producers -> Send Messages -> SQS -> Poll Messages -> Consumer

SQS Queue
    Fully managed service, used to decouple applications
    Attributes:
        Unlimited throughput, unlimited number of messages in queue
        Default retention of messages: 4 days, maximum of 14 days
        Low latency (< 10ms on publish and receive)
        Limitation of 256KB per message sent
    Can have duplicate messages (at least once delivery, occasionally)
    Can have out of order messages (best effor ordering)

Producing Messages
    Produced to SQS using the SDK (SendMessage API)
    The message is persisted in SQS until a consumer deletes it
    Message retention: default 4 days, up to 14 days
    Example 
        Send an order to be processed
            Order ID
            Customer ID
            Any attributes you want
    SQL Standard: Unlimited throughput

Consuming Messages
    Consumers (running on EC2 instances, servers, or AWS Lambda)
    Poll SQS for messages (receive up to 10 messages at a time)
    Process the messages (example: insert the message into an RDS Database)
    Delete the messages using the DeleteMessage API

Message Visibility Timeout
    After a message is polled by a consumer, it becomes invisible to other consumers
    By default, the "message visibility timeout" is 30 seconds
    That means the message has 30 seconds to be processed
    After the message visibility timeout is over, the message is "visible" in SQS
    If a message is not processed within the visibility timeout, it will be processed twice
    A consumer could call the ChangeMessageVisibility API to get more time
    If visibility timeout is high (hours), and consumer crashes, re-processing will take time
    If visibility timeout is too low (seconds), we may get duplicates

SQS - Long Polling
    When a consumer requests messages from the queue, it can optionally "wait" for messages to arrive if there are non in the queue
    LongPolling decreases the number of API calls made to SQS while increasing the efficiency and reducing latency of your application
    The wait time can be between 1 sec to 20 sec (prefer 20 sec)
    Long Polling is preferable to Short Polling
    Can be enabled at the queue level or at the API level using WaitTimeSeconds

SQS - FIFO Queue
    First In First Out (FIFO)
    Limited throughput: 300 msg/s without batching, 3000 msg/s with
    Exactly-once send capability (by removing duplicates using Deduplication ID)
    Messages are processed in order by the consumer
    Ordering by Message Group ID (all messages in the same group are ordered) - mandatory parameter

SQS with Auto Scaling Group (ASG)
    SQS as a buffer to database writes
        requests -> Auto-Scaling (Enqueue message) -> Send Message -> SQS Queue (infinitely scalable) -> Auto-Scaling (Dequeue message) -> Insert into Database
    SQS to decouple between application tiers
        requests -> Auto-Scaling (Front-end web app) -> Send Message -> SQS Queue (infinitely scalable) -> Receive Messages -> Auto-Scaling (Back-end processing application)

Amazon Simple Notification Service (SNS)
    What if you want to send one message to many receivers
    Buying Service -> SNS Topic -> Email notification & Fraud Service & Shipping Service & SQS Queue
    "Event Producer" only sends message to one SNS topic
    As many "Event Receivers" (subscriptions) as we want to listen to the SNS topic notifications
    Each subscriber to the topic will get all the messages (note: new feature to filter messages)
    Up to 12,500,000 subscriptions per topic
    100,000 topics limit

Topic Publish (by SDK)
    Create a topic
    Create a subscription
    Publish to the topic

Direct Publish (for mobile apps SDK)
    Create a platform application
    Create a platform endpoint
    Publish to the platform endpoint
    Works with Google GCM, Apple APNS, Amazon ADM

SNS - Security
    Encryption
        In-flight encryption using HTTPS API
        At-rest encryption using KMS keys
        Client-side encryption if the client wants to perform ecryption/decryption itself
    Access Controls
        IAM policies to regulate access to the SNS API
    SNS Access Policies (similar to S3 bucket policies)
        Useful for cross-account access to SNS topics
        Useful for allowing other services (S3...) to write to an SNS topic

SNS + SQS: Fan Out
    Push once in SNS, receive in all SQS queues that are subscribers
    Fully decoupled, no data loss
    SQS allows for: data persistence, delayed processing and retries of work
    Ability to add more SQS subscribers over time
    Make sure your SQS queue access policy allows for SNS to write
    Cross-Region Delivery: works with SQS Queues in other regions
    Buying Service -> SNS Topic -> SQS Queue -> Fraud Service
                                L> SQS Queue -> Shipping Service

Application: S3 Events to multiple queues
    For the same combination of: event type (object create) and prefix you only can have one S3 Event rule
    If you want to send the same S3 event to many SQS queues, use fan-out
    S3 Object created -> events -> S3 -> SNS Topic -> Fan out -> SQS Queues / Lambda Function




Kinesis Data Streams
    Collect and store streaming data in real-time
    Retnetion between up to 365 days
    Ability to reprocess (replay) data by consumers
    Data can't be deleted from Kinesis (until it expires)
    Data up to 1MB (typical use case is lot of "small" real-time data)
    Data ordering guarantee for data with the same "Partition ID"
    At-rest KMS encryption, in-flight HTTPS encryption
    Kinesis Producer Library (KPL) to write an optimized producer application
    Kinesis Client Library (KCL) to write an optimized consumer application

Kinesis Data Streams - Capacity Modes
    Provisioned mode
        Choose number of shards
        Each shard gets 1MB/s in (or 1000 records per second)
        Each shard gets 2MB/s out
        Scale manually to increase or decrease the number of shards
        You pay per shard provisioned per hour
    On-demand mode
        No need to provision or manage the capacity
        Default capacity provisioned (4MB/s in or 4000 records per second)
        Scales automatically based on observed throughput peak during the last 30 days
        Pay per stream per hour & data in/out per GB

Amazon Data Firehose
    Load streaming data into S3 / Redshift / OpenSearch / 3rd Party / Custom HTTP
    Fully managed
    Near real-time
    Automatic scaling
    No data storage
    Doesn't support replay capability




Amazon MQ
    SQS, SNS are "cloud-native" services: proprietary protocols from AWS
    Traditional applications running from on-premises may use open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
    When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
    Amazon MQ is a managed broker service for RabbitMQ and ActiveMQ
    Amazon MQ doesn't "scale" as much as SQS / SNS
    Amazon MQ runs on servers, can run in Multi-AZ with failover
    Amazon MQ has both queue feature (~SQS) and topic features (~SNS)
