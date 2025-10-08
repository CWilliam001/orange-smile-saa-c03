AWS Snowball
    Data migration and edge computing device that lets you physically transfer large amounts of data between on-premises locations and AWS - especially useful where network badnwidth is limited or slow
    Highly secure, portable devices to collect and process data at the edge, and migrate data into and out of AWS
    Help migrate up to Petabytes of data
    Offline devices to perform data migrations
    If it takes more than a week to transfer over the network, use Snowball devices
    AWS will provide you a snowball physical device to load it with the data you need to upload to AWS

Edge Computing
    Proces data while it's being created on an edge location
        A truck on the road, a ship on the sea, a mining station underground
    These lcoations may have limited internet and no access to coputing power
    We setup a Snowball Edge device to do edge computing
    Snowball Edge Compute Optimized (dedicated for that use case) & Storage Optimized
    Run EC2 instances or Lambda functions at the edge
    Use case: preprocess data, machine learning, transcoding media

Snowball into Glacier
    Snowball cannot import to Glacier directly
    Must use S3 first, in combination with an S3 lifecycle policy
    Snowball => S3 => S3 Glacier

Amazon FSx
    Launch 3rd party high performance file systems on AWS
    Fully managed service

Amazon FSx for Windows (File Server)
    Fully managed Windows file system share drive
    Supports SMB protocol & Windows NTFS
    Microsoft Active Directory integration, ACLs user quotas
    Can be mounted on Linux EC2 instances
    Supports Microsoft's Distributed File System (DFS) Namespaces (group files across multiple FS)
    Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
    Storage options:
        SSD - latency sensitive workloads (databases, media processing, data analytics, ...)
        HDD - broad spectrup of workloads (home directory, CMS, ...)
    Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
    Can be configured to be Multi-AZ (high availability)
    Data is backed-up daily to S3

Amazon FSx for Lustre
    Lustre is a type of parallel distributed file system, for large scale computing
    The name Lustre is derived from "Linux" and "Cluster"
    Machine Learning, HPC
    Video Processing, Financial Modeling, Electronic Design Automation
    Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
    Storage options:
        SSD - low latency , IOPS intensive workloads, small & random file operations
        HDD - throughput-intensive workloads, large & sequential file operations
    Seamless integration with S3
        Can "read S3" as a file system (through FSx)
        Can write the output of the computation back to S3 (through FSx)
    Can be used from on-premises servers (VPN or Direct Connect)

FSx File System Deployment Options
    Scratch File System
        Temporary storage
        Data is not replicated (doesnt persist if file server fails)
        High burst (6x faster, 200MB/s per TB)
        Usage: short-term processing, optimize costs
    Persistent File System
        Long-term storage
        Data is replicated within same AZ
        Replace failed files wihtin minutes
        Usage: long-term processing, sensitive data

Amazon FSx for NetApp ONTAP
    Managed NetApp ONTAP on AWS
    File Ssytem compatible with NFS, SMB, iSCSI protocol
    Move workloads running on ONTAP or NAS to AWS
    Works with: Linux, Windows, MacOS, VMware Cloud on AWS, Amazon Workspaces & AppStream 2.0, Amazon EC2, EC2 and EKS
    Storage shrinks or grows automatically
    Snapshots, replication, low-cost, compression and data de-duplication
    Point-in-time instantaneous cloning (helpful for testing new workloads)

Amazon FSx for OpenZFS
    Managed OpenZFS file system on AWS
    File System compatible wiht NFS (v3, v4, v4.1, v4.2)
    Move workloads running on ZFS to AWS
    Works with: Linux, Windows, MacOS, VMware Cloud on AWS, Amazon Workspaces & AppStream 2.0, Amazon EC2, EC2 and EKS
    Scale up to 1000000 IOPS < 0.5ms latency
    Snapshots, compression and low-cost
    Point-in-time instanteneous cloning (helpful for testing new workloads)






Storage Gateway
    Bridge between on-premises data and cloud data
    Use cases: 
        Disaster recovery
        Backup & restore
        Tiered storage
        On-premises cache & low-latency files access

Types of Storage Gateway
    S3 File Gateway
        Available to all S3 classes except glacier
        Application Server -> NFS or SMB -> S3 File Gateway -> HTTPS -> S3
        Configured S3 buckets are accessible using NFS and SMB protocol
        Most recntly used data is cached in the file gateway
        Use Lifecycle Policy to transfer to S3 Glacier
        Bucket access using IAM roles for each File Gateway
        SMB Protocol has integration with Active Directory (AD) for user authentication
    Volume Gateway
        Block storage using iSCSI protocol backed by S3
        Application Server -> iSCSI -> Volume Gateway -> HTTPS -> S3 -> Amazon EBS Snapshots
        Backed by EBS snapshots which can help restore on-premises volumes
        Cached volumes: low latency access to most recent data
        Stored volumes: entire dataset is on premise, scheduled backups to S3
    Tape Gateway
        Some companies have backup process using physical tapes
        With Tape Gateway, companies use the same process but in the cloud
        Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
        Backu up data using existing tape-based process (and iSCSI interface)
        Works with leading backup software vendors
        Backup Server -> iSCSI -> Media Changer / Tape Drive -> Tape Gateway -> HTTPS -> Virtual Tapes stored in S3 -> Archived Tapes stored in Amazon Glacier





AWS Transfer Family
    A fully-managed service for the file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
    Supported Protocols
        AWS Transfer for FTP (File Transfer Protocol (FTP))
        AWS Transfer for FTPS (File Transfer Protocol over SSL (FTPS))
    Managed infrastructure, Scalable, Reliable, Highly Available (multi-AZ)
    Pay per provisioned endpoint per hour + data transfers in GB
    Store and manage users credentials within the service
    Integrate with existing authentication systems (Microsoft Active Directory, LDAP, Okta, Amazon Cognito, custom)
    Usage: sharing files, public datasets, CRM, ERP
    Users -> Route 53 -> AWS Transfer Family -> IAM Role -> S3 / EFS




AWS DataSync
    Move large amount of data to and from:
        On-premises - needs agent
        AWS to AWS - no agent needed
    Can synchronize to:
        Amazon S3
        Amazon EFS
        Amazon FSx
    Replication tasks can be scheduled hourly, daily, weekly
    File permissions and metadata are preserved
    Transfer between AWS storage services
    NFS or SMB Server -> AWS DataSync Agent (AWS Snowcone (agent pre-installed)) -> TLS -> AWS DataSync -> S3 / EFS / FSx



Storage Comparision
S3: Object Storage
S3 Glacier: Object Archival
EBS Volumes: Network storage for one EC2 instance at a time
Instance Storage: Physical storage for your EC2 instance (high IOPS)
EFS: Network File System for Linux instances, POSIX filesystem
FSx for Windows: Network File System for Windows servers
FSx for Lustre: High Performance Computing Linux file system
FSx for NetApp ONTAP: High OS Compatibility
FSx for OpenZFS: Managed ZFS file system
Storage Gateway: S3 & FSx File Gateway, Volume Gateway (cache & stored), Tape Gateway
Transfer Family: FTP, FTPS, SFTP interface on top of Amazon S3 or Amazon EFS
DataSync: Schedule data sync from on-premises to AWS, or AWS to AWS
Snowcone / Snowball / Snowmobile: To move large amount of data to the cloud physically
Database: For specific workloads, usually with indexing and querying