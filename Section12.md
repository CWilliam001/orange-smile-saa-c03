Amazon Simple Storage Service (Amazon S3)
    Main building blocks of AWS
    Advertised as "infinitely scaling" storage
    Manay websites use Amazon S3 as a backbone
    Many AWS services use Amazon S3 as an integration as well

Amazon S3 Use cases
    Backup and storage
    Disaster Recovery
    Archive
    Hybrid Cloud Storage
    Application hosting
    Data lakes & big data analytics
    Software delivery
    Static website

Buckets
    Allows people to store objects (files) in "buckets" (directories)
    Buckets mush have a globally unique name (across all regions all accounts)
    Buckets are defined at the region level
    S3 looks like a global service but buckets are created in a region
    Naming convention
        No uppercase, No underscore
        3-63 characters long
        Not an IP
        Must start wiht lowercase letter or number
        Must NOT start with the prefix xn--
        Must NOT end with the suffix -s3alias

Objects
    Have a key
    Key is the FULL path for specific Objects
    The key is composed of prefix + object name
        s3://my-bucket/my_folder/another_folder/my_file.txt
    Theres no concept of directories within buckets
    Just keys with very long names that contain "/"
    Objects values are the content of the body:
        Max Object size is 5TB
        If uploading more than 5GB, must use "Multi-Part Upload"
    Metadata (list of text key / value pairs - system or user metadata)
    Tags (Unicode key / value pair - up to 10) - useful for security / lifecycle
    Version ID (if versioning is enabled)


Security
    JSON based policies
        Resources: buckets and objects
        Effect: Allow / Deny
        Actions: Set of API to Allow or Deny
        Principal: The account or user to apply the policy to
    User-Based
        IAM Policies - which API calls should be allowed for a specific user from IAM
    Resource-Based
        Bucket Policies - bucket wide rules from the S3 console - allows cross account
        Object Access Control List (ACL) - finer grain (can be disabled)
        Bucket Access Control List (ACL) - less common (can be disabled)
    Encryption
        Encrypt objects in S3 using encryption keys
    NOTE:
        An IAM principal can access S3 Object if:
            The user IAM permissions ALLOW it OR the resource policy ALLOWS it AND there's no explicit DENY

Static Website Hosting
    S3 can host static websites and have them accessible on the Internet
    The website URL will be:
        http://bucket-name.s3-website-aws-region.amazonaws.com
        http://bucket-name.s3-website.aws-region.amazonaws.com

Versioning
    Enabled at the bucket level
    Same key overwrite will change the version: 1, 2, 3....
    A best practice to version your buckets to:
        Protect against unintended deletes
        Easy roll back to previous version
    Any file that is not versioned prior to enabling versioning will have version "null"
    Suspending versioning does not delete the previous versions

Replication (Cross-Region Replication (CRR) & Same-Region Replication (SRR))
    To perform asynchronous replication
    Must enable Versioning in source and destination buckets
    Buckets can be in different AWS accounts
    Copying is asynchronous
    Must give proper IAM permissions to S3
    Use cases:
        CRR: Compliance, lower latency access, replication across accounts
        SRR: Log aggregation, live replication between production and test accounts
    After you enable Replication, only new objects are replicated
    Optionally, you cna replicate existing objects using S3 Batch Replication
        Replicates existing objects and objects that failed replication
    For DELETE operations
        Can replicate delete markers from source to target (optional setting)
        Delitions with a version ID are not replicated (to avoid malicious deletes)
    Theres no "chaining" of replication
        If bucket 1 has replication into bucket 2, which has replication into bucket 3
        Then objects created in bucket 1 are not replicated to bucket 3




Durability
    High durability of objects across multiple AZ
    If you store 10,000,000 objects with Amazon S3, you can on average expect to incur a loss of a single object once every 10,000 years
    Same for all storage classes

Availability
    Measures how readily available a service is
    Varies depending on storage class
        eg: S3 standard has 99.99 availability = not available 53 minutes a year

Storage Classes
    Can move between classes manually or using S3 Lifecycle configurations
    S3 Standard - General Purpose
        Used for frequently accessed data
        Low latency and high throughput
        Sustain 2 concurrent facility failures
        Use cases: Big Data analytics, mobile & gaming applications, content distribution
    For data that is less frequently accessed, but requires rapid access when needed
    Lower cost than S3 standard
        S3 Standard-Infrequent Access (IA)
            Disaster Recovery backups
        S3 One Zone-Infrequent Access
            Storing secondary backup copies of on-premise data or you can recreate
    Low-cost object storage meant for archiving / backup
    Pricing: price for storage + object retrieval cost
        S3 Glacier Instant Retrieval
            Millisecond retrieval, great for data accessed once a quarter
            Minimum storage duration of 90 days
        S3 Glacier Flexible Retrieval
            Expedited (1-5 minutes)
            Standard (3-5 hours)
            Bulk (5-12 hours)
            Minimum storage duration of 90 days
        S3 Glacier Deep Archive
            Standard (12 hours)
            Bulk (48 hours)
            Minimum storage duration of 180 days
        S3 Intelligent Tiering
            Small monthly monitoriing and auto-tiering fee
            Moves objects automatically between Access Tiers based on usage
            There are no retrieval charges in S3 Intelligent tiering
                Frequent Access tier (automatic): default tier
                Infrequent Access tier (automatic): objects not accessed for 30 days
                Archive Instant Acces tier (automatic): objects not access for 90 days
                Archive Access tier (optional):configurable from 90 days to 700++ days
                Deep Archive Access tier (optional): config from 180 days to 700++ days
        Express One Zone
            High performance, single AZ storage class
            Objects stored in a Directory Bucket (bucket in a single AZ)
            Handle 100,000 requests per second with single-digit millisecond latency
            Up to 10x better performance than S3 Standard (50% lower costs)
            High Durability and Availability
            Co-locate your storage and compute resources in the same AZ (reduces latency)
            Best integrated with SageMaker Model Training, Athena, EMR, Glue