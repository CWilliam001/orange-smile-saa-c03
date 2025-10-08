Docker
    A software development to deploy apps
    Apps are packaged in containers that can be run on any OS
    Apps run the same, regardless of where they're run
        Any machine
        No compaatibility issues
        Predictable behavior
        Less work
        Easier to maintain and deploy
        Works with any language, any OS, any technology
    Use case: Microservices architecture, life-and-shift apps from on-premises to the AWS cloud

Where are Docker images stored
    Docker images are stored in Docker Repositories
    Docker Hub
        Public repository
        Find base images for many technologies or OS
    Amazon Elastic Container Registry (ECR)
        Private repository
        Public repository

Docker vs Virtual Machines
    Docker is "sort of" a virtualization technology, but not exactly
    Resources are shared with the host => Many containers on one server

Getting Started with Docker
    Dockerfile -> Build -> Docker Image -> Run -> Docker Container

Amazon Elastic Container Service (ECS)
    Amazon's own container platfor

Amazon Elastic Kubernetes Service (EKS)
    Amazon's managed Kubernetes (open source)

Amazon Fargate
    Amazon's own Serverles container platform
    Works with ECS and with EKS

Amazon Elastic Container Registry (ECR)
    Store container images

ECS - EC2 Launch Type
    Launch Docker containers on AWS = Launch ECS tasks on ECS Clusters
    EC2 Launch Type: you must provision & maintain the infrastructure (the EC2 Instances)
    Each EC2 Instance must run the ECS Agent to register in the ECS Cluster
    AWS takes care of starting / stopping containers

ECS - Fargate Launch Type
    Launch Docker containers on AWS
    You do not provision the infrastructure (no EC2 Instances to manage)
    It's all Serverless
    You just create task definitions
    AWS just runns ECS Tasks for you based on the CPU / RAM you need
    To scale, just increase the number of tasks. Simple - no more EC2 Instances

ECS - IAM Roles for ECS
    EC2 Instance Profile (EC2 Launch Type only)
        Used by the ECS agent
        Makes API calls to ECS service
        Send container logs to CloudWatch Logs
        Pull Docker image from ECR
        Reference sensitive data in Secrets Manager or SSM Parameter Store
    ECS Task Role
        Allows each task to have a specific role
        Use different roles for the different ECS Services you run
        Task Role is defined in the task definition

ECS - Load Balancer Integrations
    ALB supported and works for most use cases
    NLB recommended only for high throughput / high performance use cases, or to pair it with AWS Private Link
    CLB supported but not recommended (no advanced features - no Fargate)

ECS - Data Volumes (EFS)
    Mount EFS file systems onto ECS tasks
    Works for both EC2 and Fargate launch types
    Tasks running in any AZ will share the same data in the EFS file system
    Fargete + EFS = Serverless
    Use case: persistent multi-AZ shared storage for your containers
    S3 cannot be mounted as a file system

ECS Service Auto Scaling
    Automatically increase/decrease the desired number of ECS tasks
    ECS Auto Scaling uses AWS Application Auto Scaling
        ECS Service Average CPU Utilization
        ECS Service Average Memory Utilization - Scale on RAM
        ALB Request Count Per Target - metric coming from the ALB
    Target Tracking - scale based on target value for a specific CloudWatch metric
    Step Scaling - scale based on a specified CloudWatch Alarm
    Scheduled Scaling - scale based on a specified date/time (predictable changes)
    ECS Service Auto Scaling (task level) =/= EC2 Auto Scaling (EC2 Instance level)
    Fargate Auto Scaling is much easier to setup (because Serverless)

EC2 Launch Type - Auto Scaling EC2 Instances
    Accommodate ECS Service Scaling by adding underlying EC2 Instances
    Auto Scaling Group Scaling
        Scale your ASG based on CPU Utilization
        Add EC2 Instances over time
    ECS Cluster Capacity Provider
        Used to automatically provision and scale the infrastructure for your ECS Tasks
        Capacity Provider paired with an Auto Scaling Group
        Add EC2 Instances when you're missing capacity (CPU, RAM...)

ECR
    Store and manage Docker images on AWS
    Private and Public repository
    Fully integrated with ECS, backed by S3
    Access is controlled through IAM (permission errors -> policy)
    Supports image vulnerability scanning, versioning, image tags, image lifecycle

EKS
    It is a way to launch managed Kubernetes clusters on AWS
    Kubernetes is an open-source system for automatic deployment, scaling and management of containerized (usually Docker) application
    It's an alternative to ECS, similar goal but different API
    EKS supports EC2 if you want to deploy worker nodes or Fargate to deploy serverless containers
    Use case: if your company is already using Kubernetes on-premises or in another cloud, and wants to migrate to AWS using Kubernetes
    Kubernetes is cloud-agnostic

EKS - Node Types
    Managed Node Groups
        Creates and manages Nodes (EC2 Instances) for you
        Nodes are part of an ASG managed by EKS
        Supports On-Demand or Spot Instances
    Self-Managed Nodes
        Nodes created by you and registered to the EKS cluster and managed by an ASG
        You can use prebuilt AMI - EKS Optimized AMI
        Suports On-Demand or Spot Instances
    AWS Fargate
        No maintenance required; No nodes managed

EKS - Data Volumes
    Need to specify StorageClass manifest on your EKS cluster
    Leverages a Container Storage Interface (CSI) compliant driver
    Support for:
        EBS
        EFS
        FSs for Lustre
        FSx for NetApp ONTAP

App Runner
    Fully managed service that makes it easy to deploy web applications and APIs at scale
    No infrastructure experience required
    Start with your source code or container image
    Automatically builds and deploy the web app
    Automatic scaling, highly available, load balancer, encryption
    VPC access support
    Connect to database, cache, and message queue services
    Use case: Web app, APIs, microservices, rapid production deployments

App2Container (A2C)
    CLI tool for migrating and modernizing Java and .NET web apps into Docker Containers
    Lift-and-shift your apps running in on-premises bare metal, virtual machines, or in any Cloud to AWS
    Accelerate modernization, no code changes, migrate legacy apps
    Generates CloudFormation templates (compute, network)
    Register generated Docker containers to ECR
    Deploy to ECS, EKS, or App Runner
    Supports pre-built CI/CD pipelines