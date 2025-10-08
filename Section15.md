AWS CloudFront
    Content Delivery Network (CDN)
    Improves read performance, content is cached at the Edge
    Imporoves users experience
    Hundreds of Edge Location, caches
    DDoS Protection, integration with Shield, AWS WAF

CloudFront vs S3 Cross Region Replication (CRR)
    CloudFront
        Global Edge network
        Files are cached for a TTL (maybe a day)
        Great for static content that must be available everywhere
    S3 CRR
        Must be setup for each region you want replication to happen
        Files are updated in near real-time
        Read only
        Great for dynamic content that needs to be available at low-latency in few regions

CloudFront - ALB or EC2 as an origin
    Using VPC Origins
        Allows you to deliver content from your applications hosted in your VPC private subnets (no need to expose them on the Internet)
        Deliver traffic to private:
            ALB
            NLB
            EC2 Instances
    Using Public Network
        End user <-> Edge Location <-> Allow Public IP of Edge Locations <-> EC2
        End user <-> Edge Location Public IPs <-> Allow Public IP of Edge Locations <-> Application Load Balancer Must be Public <-> Allow Security Group of Load Balancer <-> EC2 Instances (Can be Private)

CloudFront - Geo Restriction
    Restrict who can access your distribution
        Allowlist: Allow your user to access your content ony if they're in one of the countries on a list of approved countries
        Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of banned countries
    The "country" is defermined using a 3rd party Geo-IP database
    Use case: Copyright Laws to control access to content



CloudFront - Pricing
    Depends on different countries
    Depends on transfer size
    Reduce cost by reduce number of edge locations
    Price classes
        Price Class All: All regions - best performance
        Price Class 200: Most regions, but excludes the most expensive regions
        Price Class 100: Only the least expensive regions

CloudFront - Cache Invalidations
    In case you update the backend origin, CloudFront doesn't know about it and will only get the refreshed content after the TTL has expired
    However, you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront Invalidation
    You can invalidate all files (*) or a special path (/images/*)


Imagine you have deployed an application and have global users who want to access it directly
They go over the public Internet, which can add a lot of latency due to many hops
We wish to go as fast as possible through AWS network to minimize latency

Unicast IP: One server holds one IP address
Anycast IP: All servers hold the same IP address and the client is routed to the nearest one

AWS Global Accelerator
    Leverage the AWS internal network to route to your application
    2 Anycast IP are created for your application
    The Edge locations send the traffic to your application
    Works with Elastic IP, EC2 Instances, ALB, NLB, Public or Private
    Consistent Performance
        Intelligent routing to lowest latency and fast regional failover
        No issue with client cache (because the IP doesn't change)
        Internal AWS network
    Health Checks
        Global Accelerator performs a health check of your applications
        Helps make your application global (failover less than 1 minute for unhealthy)
        Great for disaster recovery (Thanks to the Health Checks)
    Security
        Only 2 external IP need to be whitelisted
        DDoS protection thanks to AWS Shield



AWS Global Accelerator vs CloudFront
    The both use AWS Global Network and its edge locations around the world
    Both services integrate with AWS Shield for DDoS protection

CloudFront
    Improves performance for both cacheable content (such as images and videos)
    Dynamic content (such as API acceleration and dynamic site delivery)
    Content is served at the edge

Global Accelerator
    Improves performance for a wide range of applications over TCP or UDP
    Proxying packets at the edge to applications running in one or more AWS Regions
    Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice Over IP (VOIP)
    Good for HTTP use cases that require static IP addresses
    Good for HTTP use cases that required deterministic, fast regional failover