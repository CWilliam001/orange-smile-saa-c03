ELastic Block Store (EBS)
    A network drive you can attach to your instances while they run
    Persist data even after their termination
    They can only be mounted to one instance at a time
    They are bound to a specific availability zone

Elastic Block Storage (EBS)
    A network drive use to communicate the instance, which might be a bit of latency
    It can be detached from an EC2 instance and attached to another one quickly
    Locked to an AZ
    To move it, you need to snapshot it
    Have a provisioned capacity (size in GBs and Input/Output Operations Per Second (IOPS))

EBS Snapshots
    Make a backup of you EBS volume at a point in time
    Not necessary to detach volume to do snapshot, but recommended
    Allow you to copy snapshots across AZ or region

EBS Snapshots Archive
    Move a Snapshot to an archive tier that is 75% cheaper
    Takes within 24 to 72 hours for restoring the archive

Recycle Bin for EBS Snapshots
    Setup rules to retain deleted snapshots so you can recover them after an accidental deletion
    Specify retention (from 1 day to 1 year)

Fast Snapshot Restore (FSR)
    Force full initialization of snapshot to have no latency on the first use
    Cost a lot of money





Amazon Machine Image (AMI)
    Customization of an EC2 instance
    Add your own software, configuration, OS, monitoring
    Faster boot/configuration time because all your software is pre-packaged
    AMI are built for a specific region

Types of AMI
    Public AMI
        Provided by AWS
    Self customized AMI
        Make and maintain AMI by yourself
    AWS Marketplace AMI
        AMI made from someone else (and potentialy sells)


EC2 Instance Store
    EBS volumes are network drives with good but limited performance
    If you need a High Performance Hardware Disk, use EC2 Instance Store
    Better I/O performance
    EC2 Instance Store lose their storage if they're stopped (ephemeral)
    Good for buffer / cache / scratch data / temporary content
    Risk of data loss if hardware fails
    Backup and Replication are your responsibility

EBS Volume Types
    General Purpose SSD (gp2/gp3)
        Balance price and performance for a wide variety of workloads
    Highest Performance SSD (io1/io2 Block Express)
        Suitable for mission critical low-latency or high-throughput workloads
    Low Cost HDD (st1)
        Designed for frequently accessed, throughput-intensive workloads
    Lowest cost HDD (sc1)
        Designed for less frequently accessed workloads

EBS Volumes are characterized in Size | Throughput | IOPS

Only gp2/gp3 & io1/io2 Block Express can be used as boot volumes (which serves as C drive to boot EC2 OS)



Volume Types Use Cases
General Purpose SSD
    Cost effective storage, low latency
    System boot volumes, Virtual Desktops, Development and test environments
    1GB - 16TB
    gp3
        The latest gp SSD that has baseline of 3000 IOPS and throughput of 125MB
        Can increase IOPS up to 16000 and throughput up to 1000MB independently
    gp2
        Small gp2 volumes can burst IOPS to 3000
        Size of the volume and IOPS are linked max IOPS is 16000
        3 IOPS per GB means at 5334GB we are at the max IOPS
Provisioned IOPS (POIPS) SSD
    Critical business applications with sustained IOPS performance
    Applications that need more than 16000 IOPS
    Great for databases workloads
    Support EBS Multi Attach
Hard Disk Drives (HDD)
    Canot be a boot volume
    125GB - 16TB
    Throughput Optimized HDD (st1)
        Big Data, Data Warehouses, Log Porcessing
        Max throughput 500MB - max IOPS 500
    Cold HDD (sc1)
        For data that is infrequently accessed
        Scenarios where lowest cost is important
        Max throughput 250MB max IOPS 250                                                                                                                  



EBS Multi Attach io1/io2 family
    Attach the same EBS volume to multiple EC2 instances in the same AZ
    Each instance has full read & write permissions to the high-performance volume
    Archive higher application availability in clustered Linux applications
    Applications mut manage concurrent write operations
    Up to 16 EC2 Instances at a time
    Must use a file system that's cluster aware (not XFS, EXT4)

EBS Encryption
    Data at rest is encrypted inside the volume
    All the data in flight moving between the instance and the volume is encrypted
    All snapshots are encpryted
    All volumes created from the snapshot
    Encryption and decryption are handled transparently (you have nothing to do)
    Encryption has a minimal impact on latency
    EBS Encryption leverages keys from KMS (AES-256)
    Copying an unencrypted snapshot allows encryption
    Create an EBS snapshot of your EBS volume. Copy the snapshot and tick the option to encrypt the copied snapshot. Then use the encrypted snapshot to create a new EBS volume


Elastic File System (EFS)
    Managed NFS (Network File System) that can be mounted on many EC2
    EFS works with EC2 instanes in multi-AZ
    Highly available, scalable, expensive (3x gp2), pay per use
    Suitable for content management, web serving, data sharing, Wordpress
    Use NFSv4.1 protocol
    Use security group to control access to EFS
    Compatible with Linux based AMI
    Encryption at rest using KMS
    File system scales automatically, pay per use no capacity planning





EFS Scale
    1000s of concurrent NFS clients, 10GB+ /s throughput
    Grow to Petabyte-scale network file system automatically
Performance Mode (set at EFS creation time)
    General Purpose (default) - latency-sensitive use cases (web server, CMS)
    Max I/O - higher latency, throughput, highly parallel (big data, media processing)
    Suitable for big data, media processing
Throughput Mode
    Bursting - 1TB = 59MB + burst of up to 100MB
    Provisioned - set your throughput regardless of storage size, ex: 1GB for 1TB storage
    Elastic - automatically scales throughput up or down based on your workloads
        Up to 3GB reads and 1GB writes
        Used for unpredictable workloads

Storage Classes
    Standard: Frequently accessed files
    Infrequent Access (EFS-IA): Cost to retrieve files, lower price to store
    Archive: Rarely accessed data (few times each year), 50% cheaper
    Implement lifecycle policies to move files between storage tiers

Availability and durability
    Standard: Multi-AZ great for production 
    One Zone: One AZ, great for developers, backup enabled by default, compatible with IA (EFS One Zone-IA)


EBS vs EFS