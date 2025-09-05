# AWS DevOps Interview Questions & Answers
*Based on Real Interview Experience*

## Table of Contents
1. [AWS Services Questions](#aws-services-questions)
2. [DevOps & Mobile Apps](#devops--mobile-apps)
3. [Linux Questions](#linux-questions)
4. [AWS Infrastructure Questions](#aws-infrastructure-questions)
5. [Database & Storage Questions](#database--storage-questions)
6. [Networking Questions](#networking-questions)
7. [Security Questions](#security-questions)
8. [General Interview Questions](#general-interview-questions)

---

## AWS Services Questions

### Q1. What are all AWS services have you worked on?

**Answer:**
I have hands-on experience with the following AWS services:

**Compute Services:**
- EC2 (Elastic Compute Cloud) - Virtual servers
- Lambda - Serverless functions
- ECS (Elastic Container Service) - Container orchestration
- EKS (Elastic Kubernetes Service) - Managed Kubernetes

**Storage Services:**
- S3 (Simple Storage Service) - Object storage
- EBS (Elastic Block Store) - Block storage for EC2
- EFS (Elastic File System) - Managed file storage

**Database Services:**
- RDS (Relational Database Service) - MySQL, PostgreSQL
- DynamoDB - NoSQL database
- ElastiCache - In-memory caching

**Networking:**
- VPC (Virtual Private Cloud) - Network isolation
- Route 53 - DNS service
- CloudFront - CDN
- ALB/NLB - Load balancers

**DevOps & CI/CD:**
- CodePipeline - CI/CD pipeline
- CodeBuild - Build service
- CodeDeploy - Deployment automation
- CloudFormation - Infrastructure as Code

**Monitoring & Security:**
- CloudWatch - Monitoring and logging
- IAM - Identity and Access Management
- AWS Config - Configuration management

---

## DevOps & Mobile Apps

### Q2. Have you worked on DevOps of Mobile apps?

**Answer:**
Yes, I have experience with mobile app DevOps workflows:

**Mobile DevOps Pipeline:**
1. **Source Control:** Git repositories for iOS/Android code
2. **Build Process:** 
   - iOS: Xcode build with certificates and provisioning profiles
   - Android: Gradle builds with signing keys
3. **Testing:** Automated unit tests and UI tests
4. **Distribution:** 
   - TestFlight for iOS beta testing
   - Firebase App Distribution for cross-platform testing
   - Google Play Console and App Store Connect for production

**Tools Used:**
- **CI/CD:** Jenkins, GitLab CI, or AWS CodePipeline
- **Build Tools:** Fastlane for automation
- **Testing:** Appium for cross-platform testing
- **Monitoring:** Firebase Crashlytics, AWS Mobile Analytics

**Challenges Addressed:**
- Certificate management for iOS builds
- Automated signing and provisioning
- Multi-environment deployments (dev, staging, prod)
- Device testing automation

---

## Linux Questions

### Q3. In which Linux OS, have you worked on? On what version?

**Answer:**
I have extensive experience with multiple Linux distributions:

**Primary Experience:**
- **Ubuntu:** 18.04 LTS, 20.04 LTS, 22.04 LTS
- **CentOS:** 7.x, 8.x (before EOL)
- **Amazon Linux:** AL2, AL2023
- **Red Hat Enterprise Linux (RHEL):** 7.x, 8.x

**Container Environments:**
- Alpine Linux (for lightweight Docker images)
- Debian (for specific application requirements)

**Experience Level:**
- Server administration and configuration
- Package management (apt, yum, rpm)
- System monitoring and troubleshooting
- Shell scripting (Bash, Python)
- Service management (systemd, init.d)

### Q4. What is enhanced in 24.04 Ubuntu Linux?

**Answer:**
Ubuntu 24.04 LTS (Noble Numbat) includes several key enhancements:

**System Improvements:**
- **Kernel:** Linux kernel 6.8 with better hardware support
- **Security:** Enhanced AppArmor profiles and security features
- **Performance:** Improved boot times and system responsiveness

**Software Updates:**
- **Python:** Default Python 3.12
- **PHP:** PHP 8.3 support
- **Node.js:** Updated to latest LTS version
- **Docker:** Latest Docker Engine support

**Developer Features:**
- Better container integration
- Improved WSL2 support for Windows developers
- Enhanced snap package performance
- Better support for modern development tools

**Infrastructure Features:**
- Improved cloud-init support
- Better Kubernetes integration
- Enhanced networking stack
- Improved systemd features

---

## AWS Infrastructure Questions

### Q5. What will happen if you lose .pem file in AWS?

**Answer:**
If you lose your .pem key file for EC2 instances, here are the solutions:

**Immediate Impact:**
- Cannot SSH into EC2 instances using that key pair
- Lose administrative access to affected instances

**Recovery Options:**

**Option 1: Create AMI and Launch New Instance**
```bash
# 1. Create AMI from existing instance
aws ec2 create-image --instance-id i-1234567890abcdef0 --name "recovery-ami"

# 2. Launch new instance with new key pair
aws ec2 run-instances --image-id ami-12345 --key-name new-keypair --instance-type t3.medium
```

**Option 2: Use Session Manager (if configured)**
```bash
# Connect without SSH key
aws ssm start-session --target i-1234567890abcdef0
```

**Option 3: User Data Script Recovery**
- Stop instance
- Modify user data to add new public key
- Start instance

**Prevention:**
- Store .pem files securely with backups
- Use AWS Systems Manager Session Manager
- Implement multiple access methods
- Use IAM roles instead of keys where possible

### Q6. How will you create a highly available network for apps in AWS?

**Answer:**
To create a highly available network architecture:

**1. Multi-AZ VPC Design:**
```yaml
# VPC with multiple Availability Zones
VPC: 10.0.0.0/16
  - Public Subnet AZ-1a: 10.0.1.0/24
  - Public Subnet AZ-1b: 10.0.2.0/24
  - Private Subnet AZ-1a: 10.0.3.0/24
  - Private Subnet AZ-1b: 10.0.4.0/24
```

**2. Load Balancer Setup:**
- **Application Load Balancer (ALB)** across multiple AZs
- **Network Load Balancer (NLB)** for high performance
- Health checks to route traffic only to healthy instances

**3. Auto Scaling Groups:**
```bash
# Launch Template across multiple AZs
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name web-asg \
  --launch-template LaunchTemplateName=web-template \
  --min-size 2 \
  --max-size 6 \
  --desired-capacity 4 \
  --vpc-zone-identifier "subnet-12345,subnet-67890"
```

**4. Database High Availability:**
- RDS Multi-AZ deployment
- Read replicas for read scaling
- Database clustering (Aurora)

**5. Additional Components:**
- **Route 53:** DNS failover and health checks
- **CloudFront:** Global content delivery
- **NAT Gateways:** Multiple AZs for internet access
- **ELB:** Cross-zone load balancing enabled

---

## Database & Storage Questions

### Q7. Suppose you have 100 GB of space in RDS instance and you want to reduce it to 25GB? How?

**Answer:**
**Important Note:** You cannot directly reduce RDS storage size in AWS. Here are the available approaches:

**Option 1: Data Migration (Recommended)**
```bash
# 1. Create new RDS instance with 25GB storage
aws rds create-db-instance \
  --db-instance-identifier mydb-small \
  --allocated-storage 25 \
  --db-instance-class db.t3.micro \
  --engine mysql

# 2. Export data from original RDS
mysqldump -h original-rds-endpoint -u username -p database_name > backup.sql

# 3. Import to new smaller RDS instance
mysql -h new-rds-endpoint -u username -p database_name < backup.sql

# 4. Update application connection strings
# 5. Delete original RDS instance
```

**Option 2: Snapshot and Restore**
```bash
# 1. Create snapshot of current RDS
aws rds create-db-snapshot \
  --db-instance-identifier original-db \
  --db-snapshot-identifier pre-resize-snapshot

# 2. Restore to new instance with smaller storage
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier new-smaller-db \
  --db-snapshot-identifier pre-resize-snapshot \
  --allocated-storage 25
```

**Pre-requisites:**
- Ensure actual data size is less than 25GB
- Plan for downtime during migration
- Test the process in staging environment
- Update monitoring and backup configurations

### Q8. Port number of MySQL RDS

**Answer:**
**Default MySQL RDS Port: 3306**

**Configuration Details:**
- **Standard Port:** 3306 (default)
- **Custom Port Range:** 1150-65535 (can be modified)
- **SSL Port:** Same as configured port (3306 by default)

**How to Check:**
```bash
# Using AWS CLI
aws rds describe-db-instances \
  --db-instance-identifier mydb \
  --query 'DBInstances[0].Endpoint.Port'

# Connection string example
mysql -h mydb.cluster-xyz.us-east-1.rds.amazonaws.com -P 3306 -u admin -p
```

**Security Considerations:**
- Configure Security Groups to allow port 3306
- Use VPC for network isolation
- Enable SSL/TLS encryption
- Consider using non-standard ports for additional security

---

## Networking Questions

### Q9. Difference between ALB and NLB in AWS

**Answer:**

| Feature | Application Load Balancer (ALB) | Network Load Balancer (NLB) |
|---------|--------------------------------|------------------------------|
| **OSI Layer** | Layer 7 (Application) | Layer 4 (Transport) |
| **Protocol Support** | HTTP, HTTPS, WebSocket | TCP, UDP, TLS |
| **Routing** | Content-based (URL, headers) | IP + Port based |
| **Performance** | Higher latency (~milliseconds) | Ultra-low latency (~microseconds) |
| **Throughput** | Moderate | Millions of requests/second |
| **Health Checks** | HTTP/HTTPS based | TCP/HTTP based |
| **SSL Termination** | Yes | Yes (TLS listener) |
| **Static IP** | No (DNS name only) | Yes (Elastic IP per AZ) |
| **WebSockets** | Native support | Pass-through |

**When to Use ALB:**
- Web applications with HTTP/HTTPS
- Microservices with path-based routing
- Content-based routing requirements
- SSL certificate management

**When to Use NLB:**
- High-performance applications
- Gaming applications
- IoT applications
- Static IP requirements
- Extreme performance needs

**Example ALB Configuration:**
```yaml
# ALB with path-based routing
Rules:
  - /api/* → API target group
  - /admin/* → Admin target group
  - /* → Default web target group
```

### Q10. Difference between gp2 and gp3 EBS volumes

**Answer:**

| Feature | GP2 | GP3 |
|---------|-----|-----|
| **Performance Model** | Baseline + Burst | Predictable performance |
| **IOPS** | 3 IOPS per GB (min 100, max 16,000) | 3,000 IOPS baseline, up to 16,000 |
| **Throughput** | Up to 250 MiB/s | 125 MiB/s baseline, up to 1,000 MiB/s |
| **Size Range** | 1 GiB - 16 TiB | 1 GiB - 16 TiB |
| **Cost** | $0.10 per GB/month | $0.08 per GB/month |
| **IOPS Pricing** | Included | Additional cost above 3,000 |
| **Burst Credits** | Yes (burstable) | No (consistent) |

**GP3 Advantages:**
- **Lower base cost** (20% cheaper)
- **Predictable performance** (no burst credits)
- **Independent scaling** of IOPS and throughput
- **Better for consistent workloads**

**Migration from GP2 to GP3:**
```bash
# Modify existing volume to GP3
aws ec2 modify-volume \
  --volume-id vol-1234567890abcdef0 \
  --volume-type gp3 \
  --iops 4000 \
  --throughput 250
```

### Q11. Difference between t3 and t4 instance

**Answer:**
**Note:** There is no t4 instance type in AWS. The progression is t2 → t3 → t4g.

**Comparison: t3 vs t4g (ARM-based)**

| Feature | T3 | T4g |
|---------|----|----|
| **Processor** | Intel Xeon (x86) | AWS Graviton2 (ARM) |
| **Architecture** | x86_64 | arm64 |
| **Performance** | Good baseline + burstable | 40% better price-performance |
| **Cost** | Standard pricing | Up to 20% less expensive |
| **Memory** | DDR4 | DDR4 |
| **Network** | Up to 5 Gbps | Up to 5 Gbps |
| **EBS Optimized** | Default on most sizes | Default on all sizes |

**T3 Family:**
- t3.nano, t3.micro, t3.small, t3.medium, t3.large, t3.xlarge, t3.2xlarge

**T4g Family:**
- t4g.nano, t4g.micro, t4g.small, t4g.medium, t4g.large, t4g.xlarge, t4g.2xlarge

**Considerations:**
- **T4g requires ARM-compatible software**
- **Better for:** Web servers, microservices, small databases
- **Migration:** May need application testing for ARM compatibility

---

## Security Questions

### Q12. How to avoid DDoS attacks in AWS?

**Answer:**
AWS provides multiple layers of DDoS protection:

**1. AWS Shield Standard (Free):**
- Automatic protection against common Layer 3/4 attacks
- Built into CloudFront, Route 53, and ALB
- No additional cost

**2. AWS Shield Advanced ($3,000/month):**
```bash
# Enable Shield Advanced on resources
aws shield create-protection \
  --name "WebApp-Protection" \
  --resource-arn arn:aws:elasticloadbalancing:region:account:loadbalancer/app/my-lb/1234567890abcdef
```

**3. AWS WAF (Web Application Firewall):**
```json
{
  "Rules": [
    {
      "Name": "RateLimitRule",
      "Priority": 1,
      "Action": {"Block": {}},
      "Statement": {
        "RateBasedStatement": {
          "Limit": 2000,
          "AggregateKeyType": "IP"
        }
      }
    }
  ]
}
```

**4. Architecture Best Practices:**
- **CloudFront:** Absorbs traffic at edge locations
- **Auto Scaling:** Handle traffic spikes automatically
- **Multiple AZs:** Distribute attack surface
- **ELB:** Built-in DDoS protection

**5. Monitoring and Response:**
```bash
# CloudWatch alarms for unusual traffic
aws cloudwatch put-metric-alarm \
  --alarm-name "High-Request-Rate" \
  --metric-name RequestCount \
  --threshold 10000
```

**6. Additional Measures:**
- Rate limiting at application level
- CAPTCHA for suspicious traffic
- Geo-blocking for specific regions
- Bot detection and mitigation

---

## Storage Questions

### Q13. Have you worked with EBS and can you reduce its volume size? How?

**Answer:**
**Direct Answer: No, you cannot directly reduce EBS volume size in AWS.**

**However, here are workarounds:**

**Option 1: Create New Smaller Volume**
```bash
# 1. Create snapshot of existing volume
aws ec2 create-snapshot \
  --volume-id vol-1234567890abcdef0 \
  --description "Before resize snapshot"

# 2. Create new smaller volume from snapshot (if data fits)
aws ec2 create-volume \
  --size 50 \
  --volume-type gp3 \
  --snapshot-id snap-1234567890abcdef0

# 3. Attach to instance and update mount points
```

**Option 2: File-level Migration**
```bash
# 1. Attach new smaller EBS volume
# 2. Format the new volume
sudo mkfs -t ext4 /dev/xvdf

# 3. Mount new volume
sudo mkdir /mnt/new-volume
sudo mount /dev/xvdf /mnt/new-volume

# 4. Copy data (ensure it fits)
sudo rsync -av /old-data/ /mnt/new-volume/

# 5. Update fstab and unmount old volume
```

**Option 3: Use EC2 Instance Store (if applicable)**
- For temporary data that doesn't need persistence
- Higher performance, no additional cost
- Data lost on instance termination

**Important Considerations:**
- **Cannot shrink volumes** - AWS limitation
- **Plan data size carefully** before creating volumes
- **Use monitoring** to right-size volumes initially
- **Backup data** before any migration attempts

**EBS Volume Types I've Worked With:**
- **gp2/gp3:** General purpose SSD
- **io1/io2:** Provisioned IOPS SSD
- **st1:** Throughput optimized HDD
- **sc1:** Cold HDD

---

## Route 53 Questions

### Q14. Have you worked in Route 53?

**Answer:**
Yes, I have extensive experience with Amazon Route 53:

**DNS Management:**
```bash
# Create hosted zone
aws route53 create-hosted-zone \
  --name example.com \
  --caller-reference $(date +%s)

# Create A record
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456789 \
  --change-batch file://change-batch.json
```

**Record Types Configured:**
- **A Records:** Point domain to IP address
- **CNAME Records:** Point subdomain to another domain
- **MX Records:** Email server routing
- **TXT Records:** Domain verification, SPF records
- **ALIAS Records:** Point to AWS resources (ELB, CloudFront)

**Health Checks and Failover:**
```json
{
  "Type": "A",
  "Name": "api.example.com",
  "SetIdentifier": "Primary",
  "Failover": "PRIMARY",
  "TTL": 60,
  "ResourceRecords": [{"Value": "1.2.3.4"}],
  "HealthCheckId": "12345"
}
```

**Routing Policies Used:**
- **Simple:** Single resource
- **Weighted:** Traffic distribution (A/B testing)
- **Latency-based:** Route to lowest latency region
- **Failover:** Primary/secondary setup
- **Geolocation:** Route based on user location
- **Multivalue Answer:** Multiple healthy endpoints

**Real-world Implementations:**
- Multi-region application failover
- Blue-green deployments with weighted routing
- CDN integration with CloudFront
- Load balancer integration with health checks
- Domain migration and DNS management

---

## General Interview Questions

### Q15. Why should we hire you?

**Answer:**
I bring a unique combination of technical expertise and practical experience that makes me an ideal fit for this DevOps role:

**Technical Strengths:**
- **5+ years** of hands-on AWS experience across 20+ services
- **Proven expertise** in Kubernetes, Docker, and container orchestration
- **Strong automation skills** with Infrastructure as Code (Terraform, CloudFormation)
- **CI/CD mastery** with Jenkins, GitLab CI, and AWS CodePipeline

**Problem-Solving Approach:**
- Successfully **reduced deployment times by 70%** through automated pipelines
- **Improved system reliability** from 95% to 99.9% uptime through monitoring and auto-scaling
- **Cost optimization** experience - reduced AWS costs by 30% through right-sizing and reserved instances

**Collaboration & Communication:**
- Experience working with **cross-functional teams** (developers, QA, security)
- **Mentoring junior engineers** in DevOps best practices
- **Documentation-first approach** ensuring knowledge sharing

**Continuous Learning:**
- **AWS Certified** (Solutions Architect, DevOps Engineer)
- Stay updated with latest technologies and industry trends
- **Open source contributions** and active in DevOps communities

**Business Impact:**
- Understand that DevOps is not just about tools, but about **improving business outcomes**
- Focus on **security, compliance**, and **scalability** in all solutions
- **Proactive monitoring** and incident response to minimize business disruption

**What Sets Me Apart:**
- **End-to-end ownership** mindset - from code commit to production monitoring
- **Strong troubleshooting skills** - can quickly identify and resolve complex issues
- **Customer-focused approach** - always considering the end-user impact of technical decisions

I'm excited about the opportunity to bring my expertise to your team and help drive your infrastructure and deployment strategies forward.

---

## Quick Reference Commands

### AWS CLI Commands
```bash
# EC2
aws ec2 describe-instances --instance-ids i-1234567890abcdef0
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

# RDS
aws rds describe-db-instances --db-instance-identifier mydb
aws rds create-db-snapshot --db-instance-identifier mydb --db-snapshot-identifier mydb-snapshot

# EBS
aws ec2 describe-volumes --volume-ids vol-1234567890abcdef0
aws ec2 create-snapshot --volume-id vol-1234567890abcdef0

# Route 53
aws route53 list-hosted-zones
aws route53 list-resource-record-sets --hosted-zone-id Z123456789
```

### Linux System Commands
```bash
# System monitoring
top, htop, iostat, free -h, df -h
netstat -tulpn, ss -tulpn

# Service management
systemctl status service_name
systemctl start/stop/restart/enable service_name

# File operations
rsync -av source/ destination/
tar -czf backup.tar.gz directory/
find /path -name "*.log" -mtime +7 -delete
```

---

**Remember:** Always provide specific examples from your experience when answering these questions. Good luck with your interview! 🚀