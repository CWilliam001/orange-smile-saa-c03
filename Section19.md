Serverless
    A new paradigm in which the developers don't have to manage servers anymore
    They just deploy code and functionos
    Initially Serverless == Function as a Service (FaaS)
    Serverless was pioneered by AWS Lambda but now also includes anything that managed "databases, messaging, storage, etc"
    Serverless does not mean there are no servers...
        It means you just don't manage / provision / see them

Serverless in AWS
    Lambda
    DynamoDB
    Cognito
    API Gateway
    S3
    SNS & SQS
    Kinesis Data Firehose
    Aurora Serverless
    Step Functions
    Fargate

Lambda vs EC2
    EC2
        Virtual Servers in the Cloud
        Limited by RAM and CPU
        Continuously running
        Scaling means intervention to add / remove servers
    Lambda
        Virtual functions - no servers to manage
        Limited by time - short executions
        Run on-demand
        Scaling is automated
    
Benefits of AWS Lambda
    Easy Pricing
        Pay per request and compute time
        Free tier of 1000000 AWS Lambda requests and 400000 GB/s of compute time
    Integrated with the whole AWS suite of services
    Integrated with many programming languages
    Easy monitoring through AWS CloudWatch
    Easy to get more resources per functions (up to 10GB of RAM)

Lambda language support
    NodeJS, PY, Java, C#, Powershell, Ruby, Custom Runtime API (community supported, excelt Rust or Golang)

Lambda Container Image
    The container image must implement the Lambda Runtime API
    ECS / Fargate is preferred for running arbitrary Docker Images

AWS Lambda Integrations Main ones
    API Gateway
    Kinesis
    DynamoDB
    S3
    CloudFront
    CloudWatch Events/EventBridge
    CloudWatch Logs
    SNS
    SQS
    Cognito

Lambda Pricing
    Pay per calls
        First 1000000 requests are free
        $0.20 per 1 million requests thereafter ($0.0000002 per request)
    Pay per duration (in increment of 1ms)
        400000GB/s of compute time per month if free
            400000 seconds if function is 1GB RAM
            3200000 seconds if function is 128MB RAM
            After that $1.00 for 600000GB/s
    It is usually very cheap to run AWS Lambda so it's very popular

Lambda Limits to Know - per region
    Execution
        Memory allocation: 128MB - 10GB (1MB incremetns)
        Maximum execution time: 900 seconds (15 minutes)
        Environment variables (4KB)
        Disk capacity in the "function container" (in /tmp): 512MB to 10GB
        Concurrency executions: 1000 (can be increased)
    Deployment
        Lambda funciton deployment size (compressed .zip): 50MB
        Size of uncompressed deployment (code + dependencie): 250MB
        Can use the /tmp directory to load other files at startup
        Size of environment variables: 4KB

Lambda Concurrency and Throttling
    Concurrency limit up to 1000 concurrent executions
    Can set a "reserved concurrency" at the function level (=limit)
    Each invocation over the concurrency limit will trigger a "Throttle"
    Throttle behavior
        If synchronous invocation => return ThrottleError - 429
        If asynchronous invocation => retry automatically and then go to DLQ
    If you need a higher limit, open a support ticket

Cold Starts & Provisioned Concurrency
    Cold Start
        New instance => code is loaded and code outside the handler run (init)
        If the init is large (code, dependencies, SDK...) this process can take some time
        First request served by new instances has higher latency than the rest
    Provisioned Concurrency
        Concurrency is allocated before the function is invoked (in advance)
        So the cold start never happens and all invocations have low latency
        Application Auto Scaling can manage concurrency (schedule or target utilization)

Lambda SnapStart
    Improves your Lambda function performance up to 10x at no extra cost for Java, Python & .NET
    When enabled, function is invoked from a pre-initialized state (no function initialization from scratch)
    When you publish a new version
        Lambda initializes your function
        Takes a snapshot of memory and disk state of the initialized function
        Snapshot is cached for low-latency access

Customization at the Edge
    Many modern applications execute some form of hte logic at the edge
    Edge function
        A code that you write and attach to CloudFront distributions
        Runs close to your users to minimize latency
    CloudFront provides two types: CloudFront Functions & Lambda@Edge
    You don't have to manage any servers, deployed globally
    Use case: customize the CDN content
    Pay only for what you use

CloudFront Functions & Lambda@Edge Use Cases
    Website Security and Privacy
    Dynamic Web Application at the Edge
    Search Engine Optimization (SEO)
    Intelligently Route Across Origins and Data Centers
    Bot Mitigation at the Edge
    Real-time Image Transformation
    A/B Testing
    User Authentication and Authorization
    User Prioritization
    User Tracking and Analytics

CloudFront Functions
    Lightweight functions written in JS
    For high-scale, latency sensitive CDN customizations
    Sub-ms startup times, millions of requests/second
    Used to change Viewer requests and responses
        Viewer Request: after CloudFront receives a request from a viewer
        Viewer Response: before CloudFront forwards the response to the viewer
    Native feature of CloudFront (manage code entirely within CloudFront)

Lambda@Edge
    Lambda functions written in NodeJS or PY
    Scales to 100s of requests/second
    Used to change CloudFront requests and responses
        Viewer Request - after CloudFront receives a request from a viewer
        Origin Request - before CloudFront forwards the request to the origin
        Origin Response - after CloudFront receives the response from the origin
        Viewer Response - before CloudFront forwards the response to the viewer
    Author your functions in one AWS Region (us-east-1), then CloudFront replicates to its locations

CloudFront Function vs Lambda@Edge - Use Cases
    CloudFront Functions
        Cache key normalization
            Transform request attributes (headers, cookies, query strings, URL) to create an optimal Cache Key
        Header manipulation
            Insert/Modify/Delete HTTP headers in the request or response
        URL rewrites or redirects
        Requuest authentication & authorization
            Create and validate user-generated tokens (JWT) to allow/deny requests
    Lambda@Edge
        Longer execution time (several ms)Adjustable CPU or memory
        You code depends on 3rd libraries (eg: AWS SDK to access other AWS services)
        Network access to use external services for processing
        File system access or access to the body of HTTP requests

Lambda by default
    By default, your Lambda function is launched outside your own VPC (in an AWS-owned VPC)
    Therefore, it cannot access resources in your VPC (RDS, ElastiCache, internal ELB, ...)

Lambda in VPC
    Must define the VPC ID, the Subnets and the Security Groups
    Lambda will create an Elastic Network Interface (ENI) in your subnets

Lambda with RDS proxy
    If Lambda functions directly access your database, they may open too many connections under high load
    RDS Proxy
        Improve scalability by pooling and sharing DB connections
        Improve availability by reducing by 66% the failover time and preserving connections
        Improve security by enforcing IAM authentication and storing credentials in Secrets Manager
    The Lambda function must be deployed in your VPC, because RDS Proxy is never publicly accessible

Invoking Lambda from RDS & Aurora
    Invoke Lambda functions from within your DB instance
    Allows you to process data events from within a database
    Supported for RDS for PSQL and Aurora MySQL
    Must allow outbound traffic to your Lambda function from within your DB instance (Public, NAT Gateway, VPC Endpoints)
    DB instance must have the required permissions to invoke the Lambda function (Lambda Resource-based Policy & IAM Policy)

RDS Event Notifications
    Notifications that tells information about the DB instance itself (created, stopped, start, ...)
    You don't have any information about the data itself
    Subscribe to the following event categories: DB instance, DB snapshot, DB Parameter Group, DB Securit Group, RDS Proxy, Custom Engine Version
    Near real-time events (up to 5 minutes)
    Send notifications to SNS or subscribe to events using EventBridge

DynamoDB
    Fully managed, highly available with replication across multiple AZs
    NoSQL database - not a relational database - with transaction support
    Scales to massive workloads, distributed database
    Millions of requests per seconds, trillions of row, 100s of TB of storage
    Fast and consistent in performance (single-digit millisecond)
    Integrated with IAM for security, authorization and administration
    Low cost and auto-scaling capabilities
    No maintenance or patching, always available
    Standard & Infrequent Access (IA) Table Class

DynamoDB - Basics
    DynamoDB is made of Tables
    Each table has a Primary Key (must be decided at creation time)
    Each table can have an infinite number of items (=rows)
    Each item has attributes (can be added over time - can be null)
    Maximum size of an item is 400KB
    Data types supported are
        Scalar Types - String, Number, Binary, Boolean, Null
        Document Types - List,  Map
        Set Types - String Set, Number Set, Binary Set
    Therefore, in DynamoDB you can rapidly evolve schemas

DynamoDB - Read/Write Capacity Modes
    Control how you manage your table's capacity (read/write throughput)
    Provisioned Mode (default)
        You specify the number of reads/writes per second
        You need to plan capacity beforehand
        Pay for provisioned Read Capacity Units (RCU) & Write Capacity Units (WCU)
        Possibility to add auto-scaling mode for RCU & WCU
    On-Demand Mode
        Read/Writes automatically scale up/down with your workloads
        No capacity planning needed
        Pay for what you use, more expensive
        Great for unpredictable workloads, steep sudden spikes

DynamoDB Accelerator (DAX)
    Fully-managed, highly available, seamless in-memory cache for DynamoDB
    Help solve read congestion by caching
    Microseconds latency for cached data
    Doesn't require application logic modification (compatible with existing DynamoDB APIs)
    5 minute TTL for cache (default)

DynamoDB - Stream Processing
    Ordered stream of item-level modifications (create/update/delete) in a table
    Use cases
        React to changes in real-time (welcome email to users)
        Real-time usage analytics
        Insert into derivative tables
        Implement cross-region replication
        Invoke AWS Lambda on changes to your DynamoDB table
    DynamoDB Streams
        24 hours retention
        Limited # of consumers
        Process using AWS Lambda Triggers, or DynamoDB Stream Kinesis adapter
    Kinesis Data Streams (newer)
        1 year retention
        High # of consumers
        Process using AWS Lambda, Kinesis Data Analytics, Kinesis Data Firehose, AWS Glue Streaming ETL...

DynamoDB Global Tables
    Make a DynamoDB table accessible with low latency in multiple-regions
    Active-Active replication
    Applications can READ and WRITE to the table in any region
    Must enable DynamoDB Streams as a pre-requisite

DynamoDB - Time To Live (TTL)
    Automatically delete items after an expiry timestamp
    Use cases: reduce stored data by keeping only current items, adhere to regulatory obligations, web session handling...

DynamoDB - Backups for disaster recovery
    Continuous backups using point-in-time recovery (PITR)
        Optionally enabled for the last 35 days
        Point-in-time recovery to any time within the backup window
        The recovery process creates a new table
    On-demand backups
        Full backups for long-term retention, until explicitely deleted
        Doesn't affect performance or latency
        Can be configured and managed in AWS Backup (enables cross-region copy)
        The recovery process creates a new table

DynamoDB - Integration with S3
    Export to S3 (must enable PITR)
        Works for any point of time in the last 35 days
        Doesn't affect the read capacity of your table 
        Perform data analysis on top of DynamoDB
        Retail snapshots for auditing
        ETL on top of S3 data before importing back into DynamoDB
        Export in DynamoDB JSON or ION format
    Import from S3
        Import CSV, DynamoDB JSON or ION format
        Doesn't consume any write capacity
        Creates a new table
        Import errors are logged in CloudWatch Logs

AWS API Gateway
    AWS Lambda + API Gateway: No infrastructure to manage
    Support for the WebSocket Protocol
    Handle API versioning (v1, v2...)
    Handle different environments (dev, test, prod...)
    Handle security (Authentication and Authorization)
    Create API keys, handle request throttling
    Swagger / Open API import to quickly define APIs
    Transform and validate requests and responses
    Generate SDK and API specifications
    Cache API responses

API Gateway - Integrations High Level
    Lambda Function
        Invoke Lambda Function
        Easy way to expose REST API backed by AWS Lambda
    HTTP
        Expose HTTP endpoints in the backend
        Example: Internal HTTP API on premise, ALB
        Why? Add rate limiting, caching, user authentications, API keys, etc...
    AWS Service
        Expose any AWS API through the API Gateway?
        Example: strt an AWS Step Function workflow, post a message to SQS
        Why? Add authentication, deploy publicly, rate control...

API Gateway - Endpoint Types
    Edge-Optimized (default): For global clients
        Requests are routed through the CloudFront Edge locations (improves latency)
        The API Gateway still lives in only one region
    Regional
        For clients within the same region
        Could manually combine with Cloud (more control over the caching strategies and the distribution)
    Private
        Can only be accessed from your VPC using an interface VPC endpoint (ENI)
        Use a resource policy to define access

API Gateway - Security
    User Authentication through
        IAM Roles (useful for internal applications)
        Cognito (identity for external users - example mobile users)
        Custom Authorized (your own logic)
    Custom Domain Name HTTPS security through integration with AWS Certificate Manager (ACM)
        If using Edge-Optimized endpoint, then the certificate must be in us-east-1
        If using Regional endpoint, the certificate must be in the API Gateway region
        Must setup CNAME or A-alias record in Route 53

AWS Step Functions
    Build serverless visual workflow to orchestrate your Lambda functions
    Features: sequence, parallel, conditions, timeouts, error handling
    Can integrate with EC2, ECS, On-premises servers, API Gateway, SQS queues, etc...
    Possibility of implementing human approval feature
    Use cases: order fulfillment, data processing, web applications, any workflow

Amazon Cognito
    Gives users an identity to interact with our web or mobile application
    Cognito User Pools
        Sign in functionality for app users
        Integrate with API Gateway & ALB
    Cognito Identity Pools (Federated Identity)
        Provide AWS credentials to users so they can access AWS resources directly
    Cognito vs IAM: "hundreds of users", "mobile users", "authenticate with SAML"

Cognito User Pools (CUP) - User Features
    Create a serverless database of user for your web & mobile apps
    Simple login: Username (or email) / password combination
    Password reset
    Email & Phone Number Verification
    Multi-factor authentication (MFA)
    Federated Identites: users from Facebook, Google, SAML

Cognito User Pools (CUP) - Integrations
    CUP integrates with API Gateway and ALB

Cognito Identity Pools (Federated Identities)
    Get identities for "users" so they obtain temporary AWS credentials
    Users source can be Cognito User Pools, 3rd party logins, etc...
    Users can then access AWS services directly or through API Gateway
    The IAM policies applied to the credentials are defined in Cognito
    They can be customized based on the user_id for fine grained control