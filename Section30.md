CloudFormation
    Declarative way of outlining your AWS Infrastructure, for any resources (most of tehm are supported)
    For example, within a CloudFormation template, you say:
        I want a security group
        I want two EC2 instances using this security group
        I want an S3 bucket
        I want a load balancer (ELB) in front of these machines
    Then CloudFormation creates those for you, in the right order, with the exact configuration that you specify


Benefits of AWS CloudFormation
    Infrastructure as code
        No resources are manually created, which is excellent for control
        Changes to the infrastructure are reviewed through code
    Cost
        Each resources within the stack is tagged with an identifier so you can easily see how much a stack costs you
        You can estimate the costs of your resources using the CloudFormation template
        Savings strategy: In Dev, you could automation deletion of templates at 5PM and recreated at 8AM, safely
    Productivity
        Ability to destroy and re-create an infrastructure on the cloud on the fly
        Automated generation of Diagram for your templates
        Declarative programming (no need to figure out ordering and orchestration)
    Don't re-invent the wheel
        Leverage existing templates on the web
        Leverage the documentation
    Supports (almost) all AWS resources
        Everything we'll see in this cource is supported
        You can use "custom resources" for resources that are not supported

CloudFormation + Infrastructure Composer
    We can see all the resources
    We can see the relations between the components

When to use CloudFormation
    When we have Infrastructure as code
    When we need to repeat an architecture in different environments, different regions or even different AWS accounts

CloudFormation - Service Role
    IAM role that allows CloudFormation to create/update/delete stack resources on your behalf
    Give ability to users to create/update/delete the stack resources even if they don't have permissions to work with the resources in the stack
    Use cases:
        You want to achieve the least privilege principle but you don't want to give the user all required permissions to create the stack resources
    User must have iam:PassRole permissions

Amazon Simple Email Service (Amazon SES)
    Fully managed service to send emails securely, globally and at scale
    Allow inbound/outbound emails
    Reputation dashboard, performance insights, anti-spam feedback
    Provides statistics such as email deliveries, bounces, feedback loop results, email open
    Supports DomainKeys Identified Mail (DKIM) and Sender Policy Framework (SPF)
    Flexible IP deployment: shared, dedicated, and customer-owned IPs
    Send emails using your application using AWS Console, APIs, or SMTP
    Use cases: transactional, marketing and bulk email communications

Amazon Pinpoint
    Scalable 2-way (outbound/inbound) marketing communications service
    Supports email, SMS, push, voice and in-app messaging
    Ability to segment and personalize messages with the right content to customers
    Possibility to receive replies
    Scales to billions of messages per day
    Use cases: run campaings by sending marketing bulk, transactional SMS messages
    Versus Amazon SNS or Amazon SES
        In SNS & SES you managed each message's audience, content, and delivery schedule
        In Pinpoint, you create message templates, delivery schedules, highly-targeted segments, and full campaigns

Systems Manager - SSM Session Manager
    Allows you to start a secure shell on your EC2 and on-premises servers
    No SSH access, bastion hosts, or SSH keys needed
    No port 22 needed (better security)
    Supports Linux, macOS, and Windows
    Send session log data to S3 or CloudWatch logs

System Manager - Run Command
    Execute a document (=script) or just run a command
    Run command across multiple instances (using resource groups)
    No need for SSH
    Command Output can be show in the AWS Console, sent to 3
    Send notifications to SNS abount comand status (In progress, Success, Failed)
    Integrated with IAM & CloudTrail
    Can be invoked using EventBridge

System Manager - Patch Manager
    Automates the process of patching managed instances
    OS updates, application updates, security updates
    Supports EC2 instances and on-premises servers
    Supports Linux, macOS, and Windows
    Patch on-demand or on a schedule using Maintenance Windows
    Scan instancess and generate patch compliance report (missing patches)

System Manager - Maintenance Windows
    Defines a schedule for when to perform actions on your instances
    Example: OS patching, updating drivers, installing software, ...
    Maintenance Window contains
        Schedule
        Duration
        Set of registered instances
        Set of registered tasks

System Manager - Automation
    Simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources
    Examples: restart instances, create an AMI, EBS snapshot
    Automation Runbook - SSM Documents to define actions preformed on your EC2 instances or AWS resources (pre-defined or custom)
    Can be triggered using
        Manually using AWS Console, AWS CLI or SDK
        Amazon EventBridge
        On a schedule using Maintenance Windows
        By AWS Config for rules remediations

Cost Explorer
    Visualize, understand, and manage your AWS costs and usage over time
    Create custom reports that analyze cost and usage data
    Analyze your data at a high level: total costs and usage across all accounts
    Or Monthly, hourly, resource level granularity
    Choose an optimal Savings Plan (to lower prices on your bill)
    Forecast usage up to 12 months based on previous usage

AWS Cost Anomaly Detection
    Continuously monitor your cost and usage using ML to detect unusual spends
    It learns your unique, historic spend patterns to detect one-time cost spike and/or continuous cost increases (you don't need to define thresholds)
    Monitor AWS services, member accounts, cost allocation tags, or cost categories
    Sends you the anomaly detection report with root-cause analysis
    Get notified with individual alerts or daily/weekly summary (using SNS)

AWS Outposts
    Hybrid Cloud: business that keep an on-premises infrastructure alongside a cloud infrastructure
    Therefore, two ways of dealing with IT systems:
        One for the AWS cloud (using the AWS console, CLI, and AWS APIs)
        One for their on-premises infrastructure
    AWS Outposts are "server racks" that offers the same AWS infrastructure, services, APIs & tools to build your own applications on-premises just as in the cloud
    AWS will setup and maange "Outpost Racks" within your on-premises infrastructure and you can start leveraging AWS services on-premises
    You are responsible for the Outposts Rack physical security
    Benefits:
        Low-latency access to on-premises systems
        Local data processing
        Data residency
        Easier migration from on-premises to the cloud
        Fully managed service
    Some services that work on Outposts
        EC2
        EBS
        S3
        EKS
        ECS
        RDS
        EMR

AWS Batch
    Fully managed batch processing at any scale
    Efficiently run 100000 of computing batch jobs on AWS
    A "batch" job is a job with a start and an end (opposed to continuous)
    Batch will dynamically launch EC2 instances or Spot Instances
    AWS Batch provisions the right amount of compute / memory
    You submit the schedule batch jobs and AWS Batch does the rest
    Batch jobs are defined as Docker Images and run on ECS
    Helpful for cost optimizations and focusing less on infrastructure


Batch vs Lambda
    Lambda:
        Time limit: 15 min
        Limited runtimes: Get access to a few programming languages
        Limited temporary disk space
        Serverless
    Batch
        No time limit
        Any run time as long as it's packaged as a Docker Image
        Rely on EBS / Instance store for disk space
        Relies on EC2 (can be managed by AWS)

Amazon AppFlow
    Fully managed integration service that enables you to securely transfer data between Software-as-a-Service (SaaS) applications and AWS
    Sources: Salesforce, SAP, Zendesk, Slack, and ServiceNow
    Destinations: AWS services like S3, Redshift or non AWS such as SnowFlake and Salesforce
    Frequency: on a schedule, in response to events, or on demand
    Data transformation capabilities like filtering and validation
    Encrypted over the public Internet or privately over AWS PrivateLink
    Don't spend time writing the intergrations and leverage APIs immediately

AWS Ampify - Web and Mobile applications
    A set of tools and services that helsp you develop and deploy scalable full stack web and mobile applications
    Authentication, Storage, API (REST, GraphQL), CI/CD, PubSub, Analytics, AI/ML Predictions, Monitoring...
    Connect your source code from GitHub, AWS CodeCommit, BitBucket, GitLab, or upload directly
    Connect frontend to backend using Amplify Frontend Libraries <-> Configure backend using Amplify CLI

Instance Scheduler on AWS
    AWS solution deployed through CloudFormation (not a service)
    Automatically start/stop your AWS services to reduce costs (up to 70%)
    Example: stop company's EC2 Instances outside business hours
    Supports EC2 Instances, EC2 Auto Scaling Groups, and RDS Instances
    Schedules are managed in a DynamoDB table
    Uses resources' tags and Lambda to stop/start Instances
    Supports cross-acocunt and cross-region resources
    