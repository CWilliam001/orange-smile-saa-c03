CloudWatch Metrics
    Provides metrics for every services in AWS
    Metric is a variable to monitor (CPUUtilization, NetworkIn)
    Metrics belong to Namespaces
    Dimension is an attribute of a metric (instance id, environment, etc...)
    Up to 30 dimensions per metric
    Metrics have timestamps
    Can create CloudWatch dashboards of metrics
    Can create CloudWatch Custom Metrics (for the RAM for example)

CloudWatch Metric Streams
    Continually stream CloudWatch metrics to a destination of your choice with near-real-time delivery and low latency
        Amazon Kinesis Data Firehose (and then its destinations)
        3rd party service provider: Datadog, Dynatrace, New Relic, Splunk, Sumo Logic
    Option to filter metrics to only stream a subset of them

CloudWatch Logs
    Log groups: Arbitrary name, usually representing an application
    Log stream: Instances within application / log files / containers
    Can define log expiration policies (never expire, 1 day to 10 years)
    CloudWatch Logs can send logs to
        S3
        Kinesis Data Streams
        Firehose
        Lambda
        OpenSearch
    Logs are encrypted by default
    Can setup KMS-based encryption with your own keys

CloudWatch Logs - Sources
    SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
    Elastic Beanstalk: Collection of logs from application
    ECS: Collection from containers
    Lambda: Collection from function logs
    VPC Flow Logs: VPC specific logs
    API Gateway
    CloudTrail based on filter
    Route 53: Log DNS queries

CloudWatch Logs Insights
    Search and analyze log data stored in CloudWatch Logs
    Example: find a specific IP inside a log, count occurrences of "ERROR" in your logs
    Provides a purpose-built query language
        Automatically discovers fields from AWS services and JSON log events
        Fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit number of events...
        Can save queries and add them to CloudWatch Dashboards
    Can query multiple Log Groups in different AWS accounts
    It's a query engine, not a real-time engine

CloudWatch Logs - S3 Export
    Log data can take up to 12 hours to become available for export
    The API call is CreateExportTask
    Not near-real time or real-time... use Logs Subscriptions instead

CloudWatch Logs Subscriptions
    Get a real-time log events from CloudWatch Logs for processing and analysis
    Send to Kinesis Data Streams, Kinesis Data Firehose, or Lambda
    Subscription Filter - filter which logs are events delivered to your destination
    Cross-Acocunt Subscription - send log events to resources in a different AWS account (KDS, KDF)

CloudWatch Logs for EC2
    By default, no logs from your EC2 machine will go to CloudWatch
    You need to run a CloudWatch agent on EC2 to push the log files you want
    Make sure IAM permissions are correct
    The CloudWatch log agent can be setup on-premises too

CloudWatch Logs Agent & Unified Agent
    For virtual servers (EC2 instances, on-premise servers...)
    CloudWatch Logs Agent
        Old version of the agent
        Can only send to CloudWatch Logs
    CloudWatch Unified Agent
        Collect additional system-level metrics such as RAM, processes, etc...
        Collect logs to send to CloudWatch Logs
        Centralized configuration using SSM Parameter Store

CloudWatch Unified Agent - Metrics
    Collected directly on your Linux server / EC2 Instance
    CPU (active, guest, idle, system, user, steal)
    Disk Metrics (free, used, total), Disk IO (writes, reads, bytes, IOPS)
    RAM (free, inactive, used, total, cached)
    Netstat (number of TCP and UDP connections, net packets, bytes)
    Processes (total, dead, bloqued, idle, running, sleep)
    Swap Space (free, used, used %)
    Reminder: out-of-the box metrics for EC2 - disk, CPU, network (high level)

CloudWatch Alarms
    Trigger notifications for any metric
    Various options (sampling, %, max, min, etc...)
    Alarm States
        OK
        INSUFFICIENT_DATA
        ALARM
    Period
        Length of time in seconds to evaluate the metric
        High resolution custom metrics: 10 sec, 30 sec or multiplies of 60 sec

CloudWatch Alarm Targets
    Stop, Terminate, Reboot, or Recover an EC2 Instance
    Trigger Auto Scaling Action
    Send notification to SNS (from which you can do pretty much anything)
    
CloudWatch Alarms - Composite Alarms
    CloudWatch Alarms are on a single metric
    Composite Alarms are monitoring the states of multiple other alarms
    AND and OR conditions
    Helpful to reduce "alarm noise" by creating complex composite alarms

EC2 Instance Recovery
    Status Check
        Instance status => Check the EC2 VM
        System status => Check the underlying hardware
        Attached EBS status => Check attached EBS Volumes
        Recovery: Same Private, Public, Elastic IP, metadata, placement group

CloudWatch Alarm: good to know
    Alarms can be created based on CloudWatch Logs Metrics Filters
    To test alarms and notifications, set the alarm state to Alarm using CLI
        aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value ALARM --state-reason "testing-purposes"

CloudWatch Events (EventBridge)
    Schedule: Cron jobs (scheduled scripts)
    Event Pattern: Event rules to react to a service doing something
    Trigger Lambda functions, send SQS/SNS messages

CloudWatch Events Rules Example
    EC2 Instance: Start Instance
    CodeBuild: Failed Build
    S3 Event: Upload Object
    Trusted Advisor: New Finding
    CloudTrail: Any API Call
    Schedule or Cron: Every 4 hours

CloudWatch Events Destinations Example
    When there are rules being triggered it will go through the Filter events (optional) to CloudWatch Events, then CloudWatch Events will generate a JSON rule to the selected destinations to handle the process
    Compute: Lambda, AWS Batch, ECS Task
    Integration: SQS, SNS, Kinesis Data Steams
    Orchestration: Step Functions, CodePipeline, CodeBuild

CloudWatch Event bus
    Default Event Bus: AWS Services (EC2, Lambda, S3)
    Partner Event Bus: Zendesk, Datadog
    Custom Event Bus: Custom Apps
    Event buses can be accessed by other AWS accounts using Resource-based Policies
    You can archive events (all/filter) sent to an event bus (indefinitely or set period)
    Ability to replay archived events

CloudWatch Event - Schema Registry
    CloudWatch Event can analyze the events in your bus and infer the schema
    The Schema Registry allows you to generate code for your application, that will know in advance how data is structured in the event bus
    Schema can be versioned

CloudWatch Event - Resource-based Policy
    Manage permissions for a specific Event Bus
    Example: allow/deny events from another AWS account or AWS region
    Use case: aggregate all events from your AWS Organizationin a single AWS account or AWS Region

CloudWatch Container Insights
    Collect, aggregate, summarize metrics and logs from containers
    Available for containers on
        ECS
        EKS
        Kubernetes platforms on EC2
        Fargate (both for ECS and EKS)
    In Amazon EKS and Kuberntes, CloudWatch Insights is using a containerized version of the CloudWatch Agent to discover containers

CloudWatch Lambda Insights
    Monitoring and troubleshooting solution for serverless applications running on Lambda
    Collects, aggregates, and summarizes system-level metrics including CPU time, memory, disk, and network
    Collect, aggregates, and summarizes diagnostic information such as cold stats and Lambda worker shutdowns
    Lambda Insights is provided as a Lambda Layer

CloudWatch Contributor Insights
    Analyze log data and create time series that display contributor data
        See metrics about the top-N contributors
        The total number of unique contributors, and their usage
    This helps you find top talkers and understand who or what is impacting system performance
    Works for any AWS-generated logs (VPC, DNS, etc...)
    Example: You can find bad hosts, identify the heaviest network users, or find the URLs that generate the most errors

CloudWatch Application Insights
    Provides automated dashboards that show potential problem with monitored applications, to help isolate ongoing issues
    Your applications run on EC2 Instances with select technologies only (Java, .NET, Microsoft IIS Web Server, databases...)
    And you can use other AWS resources such as EBS, RDS, ELB, ASG, Lambda, SQS, DynamoDB, S3, ECS, EKS, SNS, API Gateway
    Powered by SageMaker
    Enhanced visibility into your application health to reduce the time it will take you to troubleshoot and repair your applications
    Findings and alerts are sent to CloudWatch Events and SSM OpsCenter

CloudWatch Insights and Operational Visibility
    CloudWatch Container Insights
        ECS, EKS, Kubernetes on EC2, Fargate, needs agent for Kubernetes
        Metrics and logs
    CloudWatch Lambda Insights
        Detailed metrics to troubleshoot serverless applications
    CloudWatch Contributors Insights
        Find "Top-N" Contributors through CloudWatch Logs
    CloudWatch Application Insights
        Automatic dashboard to troubleshoot your application and related AWS Services

CloudTrail
    Provides governance, compliance and audit for your AWS Account
    CloudTrail is enabled by default
    Get an history of events / API calls made within your AWS Account by:
        Console
        SDK
        CLI
        AWS Services
    Can put logs from CloudTrail into CloudWatch Logs or S3
    A trail can be applied to All Regions (default) or a single Region
    If a resource is deleted in AWS, investigate CloudTrail first

CloudTrail Events
    Management Events
        Operations that are performed on resources in your AWS account
        Example:
            Configuring security (IAM AttachRolePolicy)
            Configuring rules for routing data (EC2 CreateSubnet)
            Setting up logging (AWS CloudTrail CreateTrail)
        By default, trails are configured to log management events
        Can separate Read Events (that don't modify resources) from Write Events (that may modify resources)
    Data Events
        By default, data events are not logged (Because high volume operations)
        S3 object-level activity (eg: GetObject, DeleteObject, PutObject): can separate Read and Write Events
        AWS Lambda function execution activity (the Invoke API)
    CloudTrail Insights Events
        Enable CloudTrail Insights to detect unusual activity in your account
            Inaccurate resource provisioning
            Hitting service limits
            Bursts of AWS IAM actions
            Gaps in periodic maintenance activity
        CloudTrail Insights analyzes normal management events to create a baseline and then continuously analyzes write events to detect unusual patterns
            Anomalies appear in the CloudTrail console
            Event is sent to S3
            A CloudWatch Events is generated (for automation needs)

CloudTrail Events Retention
    Events are stored for 90 days in CloudTrail
    To keep events beyond this period, log them to S3 and use Athena

AWS Config
    Helps with auditing and recording compliance of your AWS resources
    Helps record configurations and changes over time
    Questions that can be solved by AWS Config:
        Is there unrestricted SSH access to my security groups?
        Do my buckets have any public access?
        How has my ALB configuration changed over time?
    You can receive alerts (SNS notifications) for any changes
    AWS Config is a per-region service
    Can be aggregated across regions and accounts
    Possibility of storing the configuration data into S3 (analyzed by Athena)

Config Rules
    Can use AWS managed config rules (over 75)
    Can make custom config rules (must be defined in AWS Lambda)
        Example:
            Evaluate if each EBS disk is of type GP2
            Evaluate if each EC2 instance is t2.micro
    Rules can be evaluated / triggered:
        For each config change
        AND / OR: At regular time intervals
    AWS Config Rules does not prevent actions from happening (no deny)
    Pricing: NO FREE TIER, $0.003 per configuration item recorded per region, $0.001 per config rule evaluation per region

Config Rules - Remediations
    Automate remediation of non-compliant resources using SSM Automation Documents
    Use AWS-Managed Automation Documents or create custom Automation Documents
        Tip: you can create custom Automation Documents that invokes Lambda function
    You can set Remediation Retries if the resource is still non-compliant after auto remediation

Config Rules - Notifications
    Use CloudWatch Events to trigger notifications when AWS resources are non-compliant
    Ability to send configuration changes and compliance state notifications to SNS (all events - use SNS Filtering or filter at client-side)

CloudWatch vs CloudTrail vs Config
    CloudWatch
        Performance monitoring & dashboards
        Events & Alerting
        Log Aggregateion & Analysis
    CloudTrail
        Record API calls made within your Account by everyone
        Can define trails for specific resources
        Global Service
    Config
        Record configuration changes
        Evaluate resources against compliance rules
        Get timeline of changes and compliance

For an ELB
    CloudWatch
        Monitoring Incoming connections metric
        Visualize error codes as a % over time
        Make a dashboard to get an idea of your load balancer performance
    Config
        Track security group rules for the Load Balancer
        Track configuration changes for the Load Balancer
        Ensure an SSL certificate is always assigned to the Load Balancer (compliance)
    CloudTrail
        Track who made any changes to the Load Balancer with API calls