Encryption in flight (TLS / SSL)
    Data is encrypted before sending and decrypted after receiving
    TLS certificates help with encryption (HTTPS)
    Encryption in flight ensures no MITM (man in the middle attack) can happen

Server-side encryption at rest
    Data is encrypted after being received by the server
    Data is decrypted before being sent
    It is stored in an encrypted form thanks to a key (usually a data key)
    The encryption / decryption keys must be managed somewhere, and the server must have access to it

Client-side encryption
    Data is encrypted by the client and never decrypted by the server
    Data will be decrypted by a receiving client
    The server should not be able to decrypt the data
    Could leverage Envelope Encryption

Key Management Service (KMS)
    Anytime you hear "encryption" for an AWS service, it's most likely KMS
    AWS manages encryption keys for us
    Fully integrated with IAM for authorization
    Easy way to control access to your data
    Able to audit KMS Key usage using CloudTrail
    Seamlessly integrated into most AWS services (EBS, S3, RDS, SSM)
    Never ever store your secrets in plaintext, especially in your code
        KMS Key Encryption also available through API calls (SDK, CLI)
        Encrypted secrets can be stored in the code / env

KMS Keys Types
    KMS Keys is the new name of KMS Customer Master Key
    Symmetric (AES-256 Keys)
        Single encryption key that is used to Encrypt and Decrypt
        AWS services that are integrated with KMS use Symmetric CMKs
        You never get access to the KMS Key unencrypted (must call KMS API to use)
    Asymmetric (RSA & ECC key pairs)
        Public (Encrypt) and Private (decrypt) key pair
        Used for Encrypt/Decrypt or Sign/Verify operations
        The public key is downloadable, but you can't access the Private Key unencrypted
        Use case: Encryption outside of AWS by users who can't call the KMS API

Types of KMS Keys
    AWS Owned Keys (free): SSE-S3, SSE-SQS, SSE-DDB (default key)
    AWS Managed Key (free): (aws/service-name, example: aws/rds or aws/ebs)
    Customer managed keys created in KMS: $1 / month
    Customer managed keys imported: $1 / month
    + pay for API call to KMS ($0.03 / 10000 calls)

Automatic Key rotation:
    AWS-managed KMS Key: automatic every 1 year
    Customer-managed KMS Key: (must be enabled) automatic & on-demand
    Imported KMS Key: Only manual rotation possible using alias

KMS Key Policies
    Control access to KMS keys, "similar" to S3 bucket policies
    Difference: you cannot control access without them
    Default KMS Key Policy:
        Created if you don't provide a specific KMS Key Policy
        Complete access to the key to the root user = entire AWS account
    Custom KMS Key Policy
        Define users, roles that can access the KMS key
        Define who can administer the key
        Useful for cross-account access of your KMS key

Copying Snapshots across accounts
    Create a Snapshot, encrypted with your own KMS Key (Customer Managed Key)
    Attach a KMS Key Policy to authorize cross-account access
    Share the encrypted snapshot
    (In target) Create a copy of the Snapshot, encrypt it with a CMK in your account
    Create a volume from the snapshot

KMS Multi-Region Keys
    Identical KMS keys in different AWS Regions that can be used interchangeably
    Multi-Region keys have the same key ID, key material, automatic rotation...
    Encrypt in one Region and decrypt in other Regions
    No need to re-encrypt or making cross-Region API calls
    KMS Multi-Region are NOT global (Primary + Replicas)
    Each Multi-Region key is managed independently
    Use case: Global client-side encryption, encryption on Global DynamoDB, Global Aurora

DynamoDB Global Tables and KMS Multi-Region Keys Client-Side encryption
    We cant encrypt specific attributes client-side in our DynamoDB table using the Amazon DynamoDB Encryption Client
    Combined with Global Tables, the client-side encrypted data is replicated to other regions
    If we use a multi-region key, replicated in the same region as the DynamoDB Global table, then clients in these regions can use low-latency API calls to KMS in their region to decrypt the data client-side
    Using client-side encryption we can protect specific fields and guarantee only decryption if the client has access to an API key

Global Aurora and KMS Multi-Region Keys Client-Side encryption
    We can encrypt specific attributes client'side in our Aurora table using the AWS Encryption SDK
    Combined with Aurora Global Tables, the client-side encrypted data is replicated to other regions
    If we use a multi-region key, replicated in the same region as the Global Aurora DB, then clients in these regions can use low-latency API calls to KMS in their region to decryptt the data client-side
    Using client-side encryption we can protect specific fields and guarantee only decryption if the client has access to an API key, we can protect specific fields even from database admins

S3 Replication Encryption Considerations
    Unencrypted object and objects encrypted with SSE-S3 are replicated by default
    Objects encrypted with SSE-C (Customer provided key) can be replicated
    For objects encrypted with SSE-KMS, you need to enable the option
        Specify which KMS Key to encrypt the objects within the target bucket
        Adapt the KMS Key Policy for the target key
        An IAM Role with KMS: Decrypt for the source KMS Key and KMS: Encrypt for the target KMS Key
        You might get KMS throttling errors, in which case you can ask for a Service Quotas increase
    You can use multi-region AWS KMS Keys, but they are currently treated as independent keys by S3 (the object will still be decrypted and then encrypted)

AMI Sharing Process Encrypted via KMS
    AMI in Source Account is encrypted with KMS Key from Source Account
    Must modify the image attribute to add a Launch Permission which corresponds to the specified target AWS account
    Must share the KMS Keys used to encrypted the snapshot the AMI references with the target account / IAM Role
    The IAM / User in the target account must have the permissions to DescribeKey, ReEncrypt*, CreateGrant, Decrypt
    When launching an EC2 Instance from the AMI, optionally the target account can specify a new KMS key in its own account to re-encrypt the volumes

SSM Parameter Store
    Secure storage for configuration and secrets
    Optional Seamless Encryption using KMS
    Serverless, scalable, durable, easy SDK
    Version tracking of configurations / secrets
    Security through IAM
    Notifications with CloudWatch Events
    Integration with CloudFormation

SSM Parameter Store Hierarchy
    /my-department/
        my-app/
            dev/
                db-url
                db-password
            prod/
                db-url
                db-password

Parameters Policies (for advanced parameters)
    Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
    Can assign multiple policies at a time

AWS Secrets Manager
    Newer service, meant for storing secrets
    Capability to force rotation of secrets every X days
    Automate generation of secrets on rotation (uses Lambda)
    Integration with Amazon RDS (MySQL, PSQL, Aurora)
    Secrets are encrypted using KMS
    Mostly meant for RDS integration

AWS Secrets Manager - Multi-Region Secrets
    Replicate Secrets across multiple AWS Regions
    Secrets Manager keeps read replicas in sync wiht the primary secret
    Ability to promote a read replica secret to a standalone secret
    Use case: Multi-region apps, disaster recovery strategies, multi-region DB

AWS Certificate Manager (ACM)
    Easily provision, manage, and deploy TLS Cetificates
    Provide in-flight encryption for websites (HTTPS)
    Supports both public and private TLS certificates
    Free of charge for public TLS certificates
    Automatic TLS certificate renewal
    Integrations with (load TLS certificates on)
        ELB (CLB, ALB, NLB)
        CloudFront Distributions
        APIs on API Gateway
    Cannot use ACM with EC2 (can't be extracted)

ACM - Requesting Public Certificates
    List domain names to be included in the certificate
        Fully Qualified Domain Name (FQDN): corp.example.com
        Wildcard Domain *.example.com
    Select Validation Method: DNS Validation or Email Validation
        DNS Validation is preferred for automation purposes
        Email Validation will send emails to contact addresses in the WHOIS database
        DNS Validation will leverage a CNAME record to DNS config (eg: Route 53)
    It will take a few hours to get verified
    The Public Certificate will be enrolled for automatic renewal
        ACM automatically renews ACM-generated certificates 60 days before expiry

ACM - Importing Public Certificates
    Option to generate the certificate outside of ACM and then import it
    No automatic renewal, must import a new certificate before expiry
    ACM sends daily expiration events starting 45 days prior to expiration
        The # of days can be configured
        Events are appearing in CloudWatch Events
    AWS Config has a managed rule named acm-certificate-expiration-check to check for expiring certificates (configurable numbers of days)

API Gateway - Endpoint Types
    Edge-Optimized (default): For global clients
        Requests are routed through the CloudFront Edge locations (improves latency)
        The API Gateway still lives in only one region
    Regional
        For clients within the same region
        Could manually combine with CloudFront (more control over the caching strategies and the distribution)
    Private
        Can only be accessed from your VPC using an interface VPC endpoint (ENI)
        Use a resource policy to define access

ACM - Integration with API Gateway
    Create a Custom Domain Name in API Gateway
    Edge-Optimized (default): For global clients
        Requests are routed through the CloudFront Edge locations (improves latency)
        The API Gateway still lives in only one region
        The TLS Certificate must be in the same region as CloudFront, in us-east-1
        Then setup CNAME or (better) A-Alias record in Route 53
    Regional
        For clients within the same region
        The TLS Certificate must be imported on API Gateway, in the same region as teh API Stage
        Then setup CNAME or (better) A-Alias record in Route 53

AWS Web Application Firewall (WAF)
    Protects your web applications from common web exploits (Layer 7)
    Layer 7 is HTTP (vs Layer 4 is TCP/UDP)
    Deploy on
        ALB
        API Gateway
        CloudFront
        AppSync GraphQL API
        Cognito User Pool
    Define Web Access Control List (ACL) Rules
        IP Set: up to 10000 IP addresses - use multiple Rules for more IPs
        HTTP headers, HTTP body or URI strings Protects from common attact - SQL injection and Cross-Site Scriptiong (XSS)
        Size constraints, geo-match (block countries)
        Rate-based rules (to count occurrences of events) - for DDoS Protection
    Web ACL are Regional except for CloudFront
    A rule group is a reusable set of rules that you can add to a Web ACL

WAF - Fixed IP while using WAF with a Load Balancer
    WAF does not support the NLB (Layer 4)
    We can use Global Accelerator for fixed IP and WAF on the ALB

AWS Shield: Protect from DDoS attack
    Distributed Denial of Service (DDoS) - Many requests at the same time
    AWS Shield Standard
        Free service that is activate for every AWS customer
        Provides protection from attacks such as SYN/UDP Floods, reflection attacts and other layer 3/layer 4 attacks
    AWS Shield Advanced
        Optional DDoS mitigation service ($3000 per month per organization)
        Protect against more sophisticated attack on EC2, ELB, CloudFront, Global Accelerator, and Route 53
        24/7 access to AWS DDoS response team (DRP)
        Protect against higher fees during usage spikes due to DDoS
        Shield Advanced automatic application layer DDoS mitigation automatically creates evaluates and deploys AWS WAF rules to mitigate layer 7 attacks

AWS Firewall Manager
    Manage rules in all accounts of an AWS Organization
    Security policy: common set of security rules
    WAF rules (ALB, API Gateways, CloudFront)
    Sheild Advanced (ALB, CLB, NLB, Elastic IP, CloudFront)
    Security Groups for EC2, ALB, ENI resources in VPC
    AWS Network Firewall (VPC Level)
    Route 53 Resolver DNS Firewall
    Policies are created at the region level
    Rules are applied to new resources as they are created (good for compliance) across all and future accounts in your Organization

WAF vs Firewall Manager vs Shield
    WAF, Shield and Firewall Manager are used together for comprehensive protection
    Define your Web ACL rules in WAF
    For granular protection of your resources, WAF alone is the correct choice
    If you want to use AWS WAF across accounts, accelerate WAF configuration, automate the protection of new resources, use Firewall Manager with AWS WAF
    Shield Advanced adds additional features on top of AWS WAF, such as dedicated support from the Sheild Response Team (SRT) and advanced reporting
    If you're pront to frequent DDoS attacks, consider purchasing Shield Advanced

Amazon GuardDuty
    Intellignet Threat discovery to protect your AWS Account
    Uses Machine Learning algorithms, anomaly detection, 3rd party data
    One click to enable (30 days trial), no need to isntall software
    Input data includes
        CloudTrail Events Logs - unusual API calls, unauthorized deployments
            CloudTrail Management Events - create VPC subnet, create trail, ...
            CloudTrail S3 Data events - get object, list objects, delete object, ...
        VPC Flow Logs - unusual internal traffic, unusual IP address
        DNS Logs - compromised EC2 instances sending encoded data within DNS queries
        Optional Features - EKS Audit Logs, RDS & Aurora, EBS, Lambda, S3 Data Events...
    Can setup CloudWatch Event rules to be notified in case of findings
    CloudWatch Event rules can target AWS Lambda or SNA
    Can protect against CryptoCurrency attacks (has a dedicated "finding" for it)

Amazon Inspector
    Automated Security Assessment
    For EC2 Instances
        Leveraging the AWS System Manager (SSM) agent
        Analyze against unintended network accessibility
        Analyze the running OS against known vulnerabilities
    For Container Images push to Amazon ECR
        Assessment of Container Images as they are pushed
    For Lambda Functions
        Identifies software vulnerabilities in function code and package dependencies
        Assessment of functions as they are deployed
    Reporting & integration with AWS Security Hub
    Send findings to CloudWatch Event

What does Amazon Inspector evaluate
    Only for EC2 Instances, Container Images & Lambda functions
    Continuous scanning of the infrastructure, only when needed
    Package vulnerabilities (EC2, ECR & Lambda) - database of CVE
    Network reachability (EC2)
    A risk score is associated with all vulnerabilities for prioritization

Amazon Macie
    Fully managed data security and data privacy service that uses Machine Learning and pattern matching to discover and protect your sensitive data in AWS
    Helps identify and alert you to sensitive data, such as Personally Identifiable Information (PII)