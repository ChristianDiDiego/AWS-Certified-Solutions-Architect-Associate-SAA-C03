# 🎯 Reinforcement Practice Questions - All 7 Tests Incorrect Areas

**Purpose:** Practice questions similar to ALL incorrect questions from Practice Tests 1-7  
**Total Questions:** 65+ questions covering all weak areas  
**Created:** March 6, 2026  
**Study Method:** Answer without looking at explanations, then review detailed answers

---

## 📊 Question Coverage by Test

| Test | Incorrect Count | Topics Covered |
|------|----------------|----------------|
| Test 1 | 31 questions | ECS, Route 53, VPC, CloudFormation, Transit Gateway |
| Test 2 | 20 questions | CloudFront ACM, ALB, GuardDuty, DynamoDB |
| Test 3 | 13 questions | S3 Storage Classes, Glacier, CloudFormation, DynamoDB |
| Test 4 | 16 questions | Auto Scaling, Savings Plans, Redshift, CloudWatch |
| Test 5 | 23 questions | CloudWatch Agent, ECS, FSx, ElastiCache |
| Test 6 | 13 questions | VPN, EBS, DynamoDB Streams, RDS |
| Test 7 | 19 questions | Direct Connect, DataSync, NAT Gateway, Redshift |

---

## 📋 Instructions

1. **Cover the answers section** while attempting questions
2. **Write down your answer** for each question
3. **Check your answer** after attempting
4. **Review the explanation** thoroughly if incorrect
5. **Mark questions** you got wrong for review in 24 hours
6. **Retake incorrect questions** after 1 week

---

## 🔴 SECTION 1: PRACTICE TEST 1 TOPICS

### Question 1: ECS Task Components (Similar to PT1-Q2)

A software company is migrating a microservices application to AWS. Each microservice needs specific configurations including:
- Container image from Amazon ECR
- 1 vCPU and 2GB memory allocation
- Environment variables for database connections
- Port 8080 exposed for application traffic
- IAM permissions to access S3 and DynamoDB

Which AWS component should the architect create to define these container specifications for ECS?

**A.** ECS Service definition with desired task count  
**B.** ECS Cluster configuration with EC2 launch type  
**C.** ECS Task Definition in JSON/YAML format  
**D.** ECS Container Instance with user data script

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**
- **ECS Task Definition** is the blueprint that defines container specifications
- Contains: image URL, CPU/memory, port mappings, environment variables, IAM roles
- Format: JSON or YAML
- Versioned (e.g., my-task:5 for revision 5)

**Why others are wrong:**
- **A:** Service maintains desired task count but doesn't define container specs
- **B:** Cluster is a logical grouping of tasks/services
- **D:** Container Instance is EC2 host for tasks, not the blueprint

**Key Concept:** Task Definition = Recipe; Task = Cooked meal; Service = Kitchen maintaining meals
</details>

---

### Question 2: Route 53 Health Checks for Failover (Similar to PT1-Q8)

A media streaming company has a primary website in us-east-1 and a disaster recovery site in eu-west-1. The architect needs to configure automatic DNS failover when the primary site becomes unhealthy. Health checks should monitor the primary site's availability.

What is the correct Route 53 configuration?

**A.** Create two separate health checks - one for each site, and use latency-based routing  
**B.** Create a health check for the primary site only, configure failover routing with primary and secondary records  
**C.** Create a health check for the secondary site only, and use geolocation routing  
**D.** Create calculated health checks combining both sites, use weighted routing 50/50

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**
- **Failover routing policy** requires:
  - Health check on PRIMARY record only
  - PRIMARY record (us-east-1)
  - SECONDARY record (eu-west-1, no health check needed)
- When primary fails health check, Route 53 automatically returns secondary

**Configuration:**
```
Primary Record:
- Name: www.example.com
- Type: A
- Value: 1.2.3.4 (us-east-1 IP)
- Routing: Failover - Primary
- Health Check: HC-123 ✅

Secondary Record:
- Name: www.example.com
- Type: A
- Value: 5.6.7.8 (eu-west-1 IP)
- Routing: Failover - Secondary
- Health Check: None (not required)
```

**Why others are wrong:**
- **A:** Two health checks unnecessary; latency routing doesn't provide failover
- **C:** Secondary doesn't need health check; geolocation doesn't provide automatic failover
- **D:** Calculated health checks are for combining multiple checks; weighted routing serves both sites simultaneously

**Key Concept:** Failover = Primary with health check + Secondary without health check
</details>

---

### Question 3: VPC Endpoints - Gateway vs Interface (Similar to PT1-Q15)

A financial services company has EC2 instances in private subnets that need to access Amazon S3 and Amazon SQS. The security team requires that all traffic must remain within the AWS network and not traverse the internet. The solution should be cost-effective.

Which VPC endpoint configuration should the architect implement?

**A.** Gateway endpoint for both S3 and SQS  
**B.** Interface endpoint (PrivateLink) for both S3 and SQS  
**C.** Gateway endpoint for S3, Interface endpoint for SQS  
**D.** NAT Gateway with private routes to S3 and SQS

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**VPC Endpoint Types:**

| Service | Endpoint Type | Cost | Why |
|---------|---------------|------|-----|
| **S3** | Gateway ✅ | FREE | Supported by gateway endpoints |
| **DynamoDB** | Gateway ✅ | FREE | Supported by gateway endpoints |
| **SQS** | Interface | $0.01/hour/AZ | Only interface endpoints available |
| **SNS** | Interface | $0.01/hour/AZ | Only interface endpoints available |
| **Most other AWS services** | Interface | $0.01/hour/AZ | - |

**Gateway Endpoint (S3/DynamoDB only):**
- Added to route table (target prefix list)
- No ENI created
- No hourly charge
- No data processing charge
- Scales automatically

**Interface Endpoint (PrivateLink):**
- Creates ENI in subnet
- Gets private IP address
- Costs $0.01/hour per AZ + data processing
- Required for all services except S3/DynamoDB

**Why others are wrong:**
- **A:** SQS doesn't support gateway endpoints
- **B:** More expensive; S3 has free gateway option
- **D:** NAT Gateway routes to internet, not private AWS network access

**Key Concept:** Gateway endpoints (FREE) = S3 & DynamoDB only; Everything else = Interface endpoints (PAID)
</details>

---

### Question 4: CloudFormation Cross-Stack References (Similar to PT1-Q25)

A development team manages infrastructure using multiple CloudFormation stacks. The network stack creates a VPC with public and private subnets. The application stack needs to launch EC2 instances in the subnets created by the network stack.

How should the architect enable the application stack to reference the subnet IDs from the network stack?

**A.** Use CloudFormation StackSets to deploy both stacks together  
**B.** Export subnet IDs using `Outputs` in network stack, import using `Fn::ImportValue` in application stack  
**C.** Store subnet IDs in Parameter Store, retrieve in application stack using dynamic references  
**D.** Use nested stacks with the network stack as parent

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**Cross-Stack References Pattern:**

**Network Stack (exports values):**
```yaml
Resources:
  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: us-east-1a

Outputs:
  PublicSubnet1Id:
    Description: Public Subnet 1 ID
    Value: !Ref PublicSubnet1
    Export:
      Name: NetworkStack-PublicSubnet1  # Export name must be unique in region
```

**Application Stack (imports values):**
```yaml
Resources:
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-12345678
      InstanceType: t3.micro
      SubnetId: !ImportValue NetworkStack-PublicSubnet1  # Import exported value
```

**Key Rules:**
- Export names must be unique within a region
- Cannot delete stack with exports if other stacks import them
- Export names cannot be updated without breaking dependent stacks

**Why others are wrong:**
- **A:** StackSets deploy same template to multiple accounts/regions, not for cross-stack references
- **C:** Parameter Store works but is not the native CloudFormation method
- **D:** Nested stacks are for parent-child relationship within single deployment

**Key Concept:** Outputs + Export in source stack; Fn::ImportValue in dependent stack
</details>

---

### Question 5: Transit Gateway ECMP for VPN (Similar to PT1-Q13)

A global enterprise has multiple offices connecting to AWS via Site-to-Site VPN. Each office requires high-throughput connectivity (up to 10 Gbps aggregate) to multiple VPCs in AWS. The current solution uses a single VPN connection per office (limited to 1.25 Gbps).

Which solution provides increased bandwidth while maintaining redundancy?

**A.** Use Direct Connect with multiple 10 Gbps connections  
**B.** Create Transit Gateway with multiple VPN connections using ECMP (Equal-Cost Multi-Path routing)  
**C.** Deploy multiple Virtual Private Gateways, one per VPC  
**D.** Use VPN CloudHub for multi-site connectivity

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**Transit Gateway ECMP Architecture:**

```
On-Premises Office
├── Customer Gateway 1 ────┐
│   (Public IP 1.2.3.4)     │
└── Customer Gateway 2 ────┤  Multiple VPN Connections
    (Public IP 5.6.7.8)     │  (ECMP enabled)
                            │
                            ▼
                    Transit Gateway
                    ├── VPN Connection 1: 1.25 Gbps
                    ├── VPN Connection 2: 1.25 Gbps
                    ├── VPN Connection 3: 1.25 Gbps
                    └── VPN Connection 4: 1.25 Gbps
                    Total: 5 Gbps aggregate ✅
                            │
                ┌───────────┴───────────┬───────────┐
                ▼                       ▼           ▼
            VPC-A                   VPC-B       VPC-C
```

**Key Features:**
- **ECMP:** Distributes traffic across multiple VPN tunnels equally
- **Scalability:** Add up to 50 VPN connections per Transit Gateway
- **Bandwidth:** Each VPN = 1.25 Gbps; 4 VPNs = 5 Gbps aggregate
- **High Availability:** If one tunnel fails, traffic redistributes to others

**Configuration Requirements:**
- Enable ECMP on Transit Gateway
- Configure BGP on customer gateways
- Use dynamic routing (BGP) with same AS path length

**Why others are wrong:**
- **A:** Direct Connect is expensive for this use case and requires physical setup
- **C:** Multiple VGWs don't aggregate bandwidth; each VPC separate
- **D:** VPN CloudHub is for hub-spoke between sites, doesn't increase per-office bandwidth

**Key Concept:** Transit Gateway + ECMP = Multiple VPN tunnels with aggregated bandwidth
</details>

---

## 🟠 SECTION 2: PRACTICE TEST 2 TOPICS

### Question 6: CloudFront ACM Certificate Region (Similar to PT2)

A web application team wants to use their custom domain `app.company.com` with a CloudFront distribution for HTTPS traffic. They need to request an SSL/TLS certificate from AWS Certificate Manager (ACM).

In which AWS region must the ACM certificate be requested?

**A.** The same region as the S3 origin bucket  
**B.** Any region where CloudFront has an edge location  
**C.** us-east-1 (N. Virginia) only  
**D.** The region closest to the majority of users

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**CRITICAL RULE - MEMORIZE THIS:**

```
╔════════════════════════════════════════════════╗
║  CloudFront + ACM Certificate                  ║
║  MUST be in us-east-1 (N. Virginia) ONLY      ║
║                                                ║
║  ✅ us-east-1 (ONLY this region works)        ║
║  ❌ Any other region = WILL NOT WORK          ║
╚════════════════════════════════════════════════╝
```

**Why us-east-1 Only?**
- CloudFront is a **global service** with control plane in us-east-1
- All CloudFront distributions reference certificates from us-east-1
- Edge locations worldwide pull certificate configuration from us-east-1

**Regional vs Global Service Certificates:**

| Service | Type | Certificate Region |
|---------|------|-------------------|
| **CloudFront** | Global | ✅ us-east-1 ONLY |
| **API Gateway (Edge-Optimized)** | Global | ✅ us-east-1 ONLY |
| **Application Load Balancer** | Regional | ✅ Same region as ALB |
| **API Gateway (Regional)** | Regional | ✅ Same region as API |

**Complete Setup:**
```bash
# Step 1: Request certificate in us-east-1
aws acm request-certificate \
  --region us-east-1 \              # ✅ MUST be us-east-1
  --domain-name app.company.com \
  --validation-method DNS

# Step 2: Create CloudFront distribution
aws cloudfront create-distribution \
  --origin-domain-name mybucket.s3.amazonaws.com \
  --default-root-object index.html \
  --viewer-certificate \
    ACMCertificateArn=arn:aws:acm:us-east-1:123456789012:certificate/abc-123 \
    SSLSupportMethod=sni-only
```

**Why others are wrong:**
- **A:** Origin bucket region is irrelevant to CloudFront certificate location
- **B:** Edge locations don't request certificates; they reference us-east-1 certificates
- **D:** User location doesn't affect certificate region requirement

**Key Concept:** CloudFront = Global service = us-east-1 certificate ALWAYS
</details>

---

### Question 7: ALB Target Group Health Checks (Similar to PT2)

An e-commerce application runs on EC2 instances behind an Application Load Balancer. The application has a custom health check endpoint at `/health` that returns HTTP 200 when healthy and HTTP 503 when the database connection fails.

How should the architect configure the ALB target group to properly detect unhealthy instances?

**A.** Use default health check on port 80 with path "/"  
**B.** Configure health check on port 80 with path "/health", healthy threshold 2, unhealthy threshold 2  
**C.** Use TCP health check on port 80  
**D.** Configure health check on port 443 with SSL certificate verification

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**ALB Health Check Configuration:**

```yaml
Health Check Settings:
  Protocol: HTTP                    # or HTTPS
  Port: 80                          # Application port
  Path: /health                     # ✅ Custom health endpoint
  Interval: 30 seconds              # How often to check
  Timeout: 5 seconds                # Max time for response
  Healthy Threshold: 2              # Consecutive successes to mark healthy
  Unhealthy Threshold: 2            # Consecutive failures to mark unhealthy
  Success Codes: 200                # Expected HTTP response code
```

**Health Check Flow:**

```
ALB Target Group
├── Target 1 (i-001): 10.0.1.10
│   ├── Check 1: GET /health → 200 OK ✅
│   ├── Check 2: GET /health → 200 OK ✅
│   └── Status: Healthy (2 consecutive successes)
│
├── Target 2 (i-002): 10.0.1.11
│   ├── Check 1: GET /health → 503 Error ❌
│   ├── Check 2: GET /health → 503 Error ❌
│   └── Status: Unhealthy (2 consecutive failures)
│       └── ALB stops sending traffic to this target
```

**Best Practices:**
- Use custom health check path that validates application health
- Check database connectivity, dependencies, not just HTTP server
- Set unhealthy threshold = 2 for faster failure detection
- Return specific HTTP codes: 200 = healthy, 5xx = unhealthy

**Why others are wrong:**
- **A:** "/" may not validate database connectivity; application could be unhealthy
- **C:** TCP check only validates port is open, not application health
- **D:** HTTPS adds unnecessary complexity; certificate verification not required for health checks

**Key Concept:** Custom health check path + proper status codes = accurate health detection
</details>

---

### Question 8: GuardDuty Threat Detection (Similar to PT2)

A security team needs to implement continuous threat detection for AWS accounts that can identify:
- Compromised EC2 instances communicating with known malicious IPs
- Unusual API calls indicating credential compromise
- Cryptocurrency mining activity on EC2 instances

The solution should require minimal operational overhead and no agent installation.

Which AWS service should be implemented?

**A.** AWS Config with custom conformance packs  
**B.** Amazon Macie for sensitive data detection  
**C.** AWS GuardDuty for intelligent threat detection  
**D.** Amazon Inspector for vulnerability scanning

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**GuardDuty Features:**

```
AWS GuardDuty - Intelligent Threat Detection
├── Data Sources (Automatic)
│   ├── VPC Flow Logs ✅
│   ├── CloudTrail Events ✅
│   ├── DNS Logs ✅
│   └── Kubernetes Audit Logs ✅
│
├── Threat Intelligence
│   ├── AWS threat intelligence
│   ├── CrowdStrike threat intelligence
│   ├── Proofpoint threat intelligence
│   └── Known malicious IP/domain lists
│
├── Machine Learning
│   ├── Anomaly detection
│   ├── Behavioral analysis
│   └── Baseline learning (7-14 days)
│
└── Findings (Severity levels)
    ├── HIGH: Cryptocurrency mining ⛏️
    ├── MEDIUM: Unusual API calls 🔑
    └── LOW: Port scanning 🔍
```

**Key Advantages:**
- ✅ **No agent installation** - analyzes existing AWS logs
- ✅ **No infrastructure management** - fully managed service
- ✅ **Continuous monitoring** - real-time threat detection
- ✅ **Minimal setup** - enable with one click

**Example Findings:**
1. **CryptoCurrency:EC2/BitcoinTool.B!DNS**
   - EC2 instance querying bitcoin mining pool domain
   - Severity: HIGH

2. **UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration**
   - IAM credentials used from unusual location
   - Severity: HIGH

3. **Recon:EC2/PortProbeUnprotectedPort**
   - EC2 instance being probed on multiple ports
   - Severity: LOW

**Why others are wrong:**
- **A:** Config checks compliance, not threat detection
- **B:** Macie detects sensitive data (PII, credentials) in S3, not threats
- **D:** Inspector scans for vulnerabilities in EC2/containers, not runtime threats

**Key Concept:** GuardDuty = Continuous threat detection with ML + no agents
</details>

---

## 🟡 SECTION 3: PRACTICE TEST 3 TOPICS

### Question 9: S3 Glacier Retrieval and Migration (Similar to PT3-Q4)

A media company has 300 TB of video files in S3 Glacier Deep Archive. The marketing team now needs frequent access to these files for the next 6 months. The architect wants to move files to S3 Intelligent-Tiering for automatic cost optimization.

What is the correct migration approach?

**A.** Change bucket default storage class to Intelligent-Tiering; existing objects automatically migrate  
**B.** Create S3 Lifecycle policy to transition from Glacier Deep Archive to Intelligent-Tiering  
**C.** Restore objects from Glacier Deep Archive, copy to Intelligent-Tiering during restoration, delete archived versions  
**D.** Use AWS DataSync to transfer files from Glacier to Intelligent-Tiering

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**S3 Storage Class Transition Rules:**

```
Allowed Direct Transitions:
├── Standard → Standard-IA ✅
├── Standard → Intelligent-Tiering ✅
├── Standard → Glacier Instant Retrieval ✅
├── Standard → Glacier Flexible Retrieval ✅
├── Standard → Glacier Deep Archive ✅
│
└── Glacier Deep Archive → Other classes ❌ BLOCKED
    │
    │ Must restore first!
    └── Glacier Deep Archive → Temporary Restore → Copy to new class
```

**Correct Migration Process:**

```
Step 1: Restore from Glacier Deep Archive
├── Retrieval Options:
│   ├── Standard: 12-48 hours ($0.02/GB)
│   └── Bulk: 48 hours ($0.0025/GB) ✅ Cost-effective
├── Specify days accessible: 1-365 days
└── Objects remain in Glacier + temporary accessible copy created

Step 2: Copy During Restoration Window
├── While objects are restored (accessible)
├── Copy to Intelligent-Tiering
└── Original Glacier copies still exist

Step 3: Delete Glacier Versions
└── Delete original Glacier Deep Archive versions to avoid double storage costs
```

**AWS CLI Commands:**

```bash
# Step 1: Initiate restore (bulk for cost savings)
aws s3api restore-object \
  --bucket media-archive \
  --key videos/video001.mp4 \
  --restore-request '{"Days":30,"GlacierJobParameters":{"Tier":"Bulk"}}'

# Step 2: After restoration completes, copy to Intelligent-Tiering
aws s3 cp \
  s3://media-archive/videos/video001.mp4 \
  s3://media-archive/videos/video001.mp4 \
  --storage-class INTELLIGENT_TIERING \
  --metadata-directive COPY

# Step 3: Delete original Glacier version (optional)
aws s3api delete-object \
  --bucket media-archive \
  --key videos/video001.mp4 \
  --version-id <glacier-version-id>
```

**Cost Calculation (300 TB):**
- Restoration: 300 TB × $0.0025/GB = $768 (bulk tier)
- Storage during transition: ~$3,840/month (30 days both copies)
- Final cost: $0.0125/GB/month (Intelligent-Tiering Archive tier)

**Why others are wrong:**
- **A:** Bucket default class ONLY affects new uploads, not existing objects
- **B:** Lifecycle policies cannot transition FROM Glacier classes to warmer tiers
- **D:** DataSync is for file system/cross-region transfers, not storage class changes

**Key Concept:** Glacier Deep Archive → Restore → Copy → Delete original (required workflow)
</details>

---

### Question 10: S3 Multipart Upload for Large Files (Similar to PT3)

A data analytics company needs to upload 10 TB video files to S3. Some files are 500 GB each. The uploads frequently fail after 3-4 hours due to network interruptions. The architect needs a reliable solution that can resume failed uploads without restarting from the beginning.

What should the architect implement?

**A.** Use AWS CLI `aws s3 cp` command with automatic retries  
**B.** Implement S3 Multipart Upload with parts of 100 MB each  
**C.** Use AWS DataSync for large file transfers  
**D.** Enable S3 Transfer Acceleration for faster uploads

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**S3 Multipart Upload Benefits:**

```
Single Large File: 500 GB
├── Standard Upload ❌
│   ├── Single HTTP PUT request
│   ├── If fails at 99%, restart entire upload
│   └── Maximum file size: 5 GB (API limit)
│
└── Multipart Upload ✅
    ├── Split into parts: 5,000 parts × 100 MB = 500 GB
    ├── Upload parts independently and in parallel
    ├── If part fails, retry only that part
    ├── Resume from last successful part
    └── Maximum file size: 5 TB
```

**Multipart Upload Workflow:**

```
1. Initiate Multipart Upload
   └── Receive Upload ID: abc123

2. Upload Parts (parallel)
   ├── Part 1: 0-99 MB → ETag: "etag1" ✅
   ├── Part 2: 100-199 MB → ETag: "etag2" ✅
   ├── Part 3: 200-299 MB → Failed ❌
   │   └── Retry Part 3 → ETag: "etag3" ✅
   ├── Part 4: 300-399 MB → ETag: "etag4" ✅
   └── Part 5000: 499.9-500 GB → ETag: "etag5000" ✅

3. Complete Multipart Upload
   └── S3 assembles parts into final object
```

**Implementation (AWS CLI):**

```bash
# Step 1: Initiate multipart upload
aws s3api create-multipart-upload \
  --bucket my-bucket \
  --key large-video.mp4

# Output: Upload ID = abc123

# Step 2: Upload parts (example: part 1)
aws s3api upload-part \
  --bucket my-bucket \
  --key large-video.mp4 \
  --part-number 1 \
  --body part1.bin \
  --upload-id abc123

# Output: ETag = "etag1"

# Step 3: Complete upload (with all ETags)
aws s3api complete-multipart-upload \
  --bucket my-bucket \
  --key large-video.mp4 \
  --upload-id abc123 \
  --multipart-upload '{"Parts":[{"ETag":"etag1","PartNumber":1},{"ETag":"etag2","PartNumber":2}]}'
```

**Best Practices:**
- **Part size:** 100 MB - 5 GB (100 MB recommended for large files)
- **Parallelism:** Upload 5-10 parts simultaneously for speed
- **S3 lifecycle:** Clean up incomplete uploads after 7 days

**AWS SDK (Python boto3) - Automatic Multipart:**

```python
import boto3
from boto3.s3.transfer import TransferConfig

s3 = boto3.client('s3')

# Configure multipart threshold and part size
config = TransferConfig(
    multipart_threshold=1024 * 25,      # 25 MB
    max_concurrency=10,
    multipart_chunksize=1024 * 25,      # 25 MB parts
    use_threads=True
)

# Upload automatically uses multipart for large files
s3.upload_file(
    'large-video.mp4',
    'my-bucket',
    'large-video.mp4',
    Config=config
)
```

**Why others are wrong:**
- **A:** `aws s3 cp` does use multipart internally, but question asks for specific implementation knowledge
- **C:** DataSync is for on-premises to AWS transfers, not client uploads
- **D:** Transfer Acceleration speeds up uploads but doesn't solve reliability/resume issue

**Key Concept:** Files > 100 MB → Use Multipart Upload for reliability + resume capability
</details>

---

## 🔵 SECTION 4: PRACTICE TEST 4 TOPICS

### Question 11: CloudWatch Custom Metrics with Auto Scaling (Similar to PT4-Q7)

A company runs a memory-intensive data processing application on EC2 instances managed by an Auto Scaling group. The application's performance degrades significantly when memory utilization exceeds 75%. The architect needs to configure Auto Scaling to scale out when average memory utilization across the ASG exceeds 70%.

What is the correct solution?

**A.** Enable detailed monitoring on EC2 instances; CloudWatch automatically collects memory metrics  
**B.** Install CloudWatch agent on instances; configure memory metric collection; create target tracking scaling policy using custom memory metric  
**C.** Use AWS Systems Manager to collect memory metrics; create step scaling policy  
**D.** Create Lambda function to query memory from EC2 instances; send metrics to CloudWatch

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**EC2 Default vs Custom Metrics:**

```
Default EC2 Metrics (No agent required):
├── CPU Utilization % ✅
├── Network In/Out (bytes) ✅
├── Disk Read/Write operations ✅
├── Disk Read/Write bytes ✅
└── Status Check Failed ✅

NOT Included (Requires CloudWatch Agent):
├── Memory Utilization % ❌
├── Memory Used/Available ❌
├── Disk Space % Used ❌
└── Swap Utilization ❌
```

**Why Memory is NOT a Default Metric:**
- AWS hypervisor can see CPU, network, disk I/O from outside guest OS
- Memory usage is internal to guest OS
- CloudWatch agent runs inside OS to collect memory stats

**Complete Solution:**

**Step 1: CloudWatch Agent Configuration**

```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "cwagent"
  },
  "metrics": {
    "namespace": "CWAgent",
    "aggregation_dimensions": [
      ["AutoScalingGroupName"]
    ],
    "metrics_collected": {
      "mem": {
        "measurement": [
          {
            "name": "mem_used_percent",
            "rename": "MemoryUtilization",
            "unit": "Percent"
          }
        ],
        "metrics_collection_interval": 60
      }
    }
  }
}
```

**Step 2: Target Tracking Scaling Policy**

```json
{
  "TargetTrackingScalingPolicyConfiguration": {
    "TargetValue": 70.0,
    "CustomizedMetricSpecification": {
      "MetricName": "MemoryUtilization",
      "Namespace": "CWAgent",
      "Statistic": "Average",
      "Dimensions": [
        {
          "Name": "AutoScalingGroupName",
          "Value": "data-processing-asg"
        }
      ]
    }
  }
}
```

**Installation (Launch Template UserData):**

```bash
#!/bin/bash
# Install CloudWatch agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
sudo rpm -U ./amazon-cloudwatch-agent.rpm

# Create config file (JSON above saved to /opt/aws/amazon-cloudwatch-agent/etc/config.json)

# Start agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/config.json
```

**IAM Role Permissions:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:PutMetricData",
        "ec2:DescribeTags",
        "autoscaling:Describe*"
      ],
      "Resource": "*"
    }
  ]
}
```

**Why others are wrong:**
- **A:** Detailed monitoring changes frequency (5min → 1min) but doesn't add memory metrics
- **C:** Systems Manager can collect but CloudWatch agent is the standard/easier approach
- **D:** Lambda adds complexity; CloudWatch agent is the purpose-built solution

**Key Concept:** Memory metrics → Requires CloudWatch agent inside guest OS
</details>

---

### Question 12: Savings Plans Strategy for Multi-Tier Application (Similar to PT4-Q19)

A company has the following workload characteristics:
- **Web tier:** 20 t3.large instances, steady 24/7, may change to t3a.xlarge for better price
- **Application tier:** 30 c5.xlarge instances, spiky usage (10-30 instances active)
- **Database tier:** 5 r5.2xlarge RDS instances for PostgreSQL, steady 24/7

The architect needs to maximize cost savings while maintaining flexibility.

Which pricing strategy should be used?

**A.** EC2 Instance Savings Plan for all tiers (web + app + database)  
**B.** Compute Savings Plan for all tiers  
**C.** Compute Savings Plan for web and app tiers; RDS Reserved Instances for database tier  
**D.** On-Demand for web tier; Spot Instances for app tier; RDS Reserved Instances for database tier

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**Savings Plan Types:**

```
EC2 Instance Savings Plan:
├── Savings: Up to 72% off On-Demand
├── Flexibility: ❌ Locked to instance family (e.g., c5)
├── Can change: Size, OS, tenancy
├── Cannot change: Family, region
└── Use case: Stable, predictable instance family

Compute Savings Plan:
├── Savings: Up to 66% off On-Demand
├── Flexibility: ✅ Any instance family, size, region, OS
├── Applies to: EC2, Fargate, Lambda
├── Cannot apply to: RDS, Redshift, ElastiCache ❌
└── Use case: Flexible workloads that may change families

RDS Reserved Instances:
├── Savings: Up to 69% off On-Demand
├── Flexibility: ❌ Locked to DB engine, instance class
├── Applies to: RDS only
└── Use case: Steady database workloads
```

**Correct Strategy:**

```
Web Tier (20 t3.large, may change to t3a.xlarge):
├── Use: Compute Savings Plan ✅
├── Why: Flexibility to change families (t3 → t3a)
├── Commitment: Cover baseline 20 instances
└── Savings: ~66% off On-Demand

Application Tier (10-30 c5.xlarge, spiky):
├── Use: Compute Savings Plan ✅
├── Why: Variable usage, may need different families
├── Commitment: Cover baseline 10 instances
└── Above baseline: On-Demand for spikes

Database Tier (5 r5.2xlarge RDS, steady):
├── Use: RDS Reserved Instances ✅
├── Why: Highest savings for steady RDS workload
├── Commitment: All 5 instances
├── Term: 3-year All Upfront
└── Savings: ~69% off On-Demand
```

**Cost Comparison (Monthly):**

```
Scenario 1: All On-Demand
├── Web: 20 × $0.0832/hr × 730 hrs = $1,215
├── App: 20 × $0.17/hr × 730 hrs = $2,482
├── DB: 5 × $0.636/hr × 730 hrs = $2,321
└── Total: $6,018/month

Scenario 2: Correct Strategy (Answer C)
├── Web: 20 × $0.0832 × 730 × 0.34 (66% savings) = $413
├── App: 10 × $0.17 × 730 × 0.34 + 10 × $0.17 × 730 = $1,240 + $1,241 = $2,481
├── DB: 5 × $0.636 × 730 × 0.31 (69% savings) = $719
└── Total: $3,613/month (40% overall savings) ✅

Savings: $2,405/month = $28,860/year
```

**Why others are wrong:**
- **A:** EC2 Instance Savings Plan doesn't apply to RDS; locks instance families
- **B:** Compute Savings Plan doesn't apply to RDS databases
- **D:** Spot Instances are interruptible, risky for application tier; no commitment savings

**Key Concept:** Compute SP (EC2 flexibility) + RDS RI (database savings) = Optimal multi-tier strategy
</details>

---

### Question 13: Redshift Snapshot Lifecycle Management (Similar to PT4-Q13)

A data warehouse team uses Amazon Redshift with automated daily snapshots retained for 35 days. Manual snapshots are taken monthly for compliance and must be kept for 7 years. The CFO wants to optimize snapshot storage costs.

Which solution reduces costs while meeting compliance requirements?

**A.** Reduce automated snapshot retention to 7 days; keep manual snapshots in Redshift for 7 years  
**B.** Export automated snapshots to S3 after 7 days; delete from Redshift; keep manual snapshots in Redshift  
**C.** Use automated snapshots for 35 days; copy manual snapshots to S3 after 1 year; delete from Redshift  
**D.** Reduce automated snapshots to 7 days; copy manual snapshots to S3 Glacier immediately after creation

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: D**

**Explanation:**

**Redshift Snapshot Costs:**

```
Redshift Snapshot Storage Pricing:
├── First TB: FREE (up to 100% of cluster size)
│   Example: 2 TB cluster = 2 TB free snapshot storage
│
├── Beyond free tier: $0.024/GB/month
│   Same price as active Redshift storage
│
└── S3 Storage (exported snapshots): $0.023/GB/month (Standard)
    └── S3 Glacier Deep Archive: $0.00099/GB/month
```

**Optimal Strategy:**

```
Daily Automated Snapshots:
├── Retention: 7 days (operational recovery) ✅
├── Cost: Within free tier (if <100% cluster size)
└── Use case: Quick recovery from recent issues

Monthly Manual Snapshots:
├── Created: 1st of every month
├── Immediately export to S3 Glacier Deep Archive ✅
├── Delete from Redshift after export
├── Retention: 7 years in Glacier
└── Cost: $0.00099/GB/month (96% cheaper than Redshift storage)
```

**Cost Calculation (2 TB cluster, 7 years compliance):**

```
Scenario 1: Keep all in Redshift
├── Daily snapshots (7 days): 2 TB × 7 = 14 TB
├── Monthly snapshots (84 months): 2 TB × 84 = 168 TB
├── Total: 182 TB
├── Cost: (182 TB - 2 TB free) × $24/TB = $4,320/month ❌

Scenario 2: Optimal (Answer D)
├── Daily snapshots (7 days): 2 TB × 7 = 14 TB (free tier ✅)
├── Monthly in S3 Glacier: 2 TB × 84 = 168 TB
├── Cost: 168 TB × $0.99/TB = $166/month ✅

Savings: $4,154/month = $49,848/year
```

**Implementation:**

```bash
# Create monthly manual snapshot
aws redshift create-cluster-snapshot \
  --cluster-identifier my-cluster \
  --snapshot-identifier manual-snapshot-2026-03

# Export to S3 immediately
aws redshift create-snapshot-copy-grant \
  --snapshot-copy-grant-name my-grant \
  --kms-key-id <kms-key>

# Wait for snapshot completion, then copy to S3
aws redshift-data execute-statement \
  --cluster-identifier my-cluster \
  --database dev \
  --sql "UNLOAD ('select * from ...') TO 's3://compliance-bucket/snapshots/2026-03/' IAM_ROLE 'arn:aws:iam::...' PARQUET"

# Delete Redshift snapshot after successful export
aws redshift delete-cluster-snapshot \
  --snapshot-identifier manual-snapshot-2026-03
```

**S3 Lifecycle Policy (for exported snapshots):**

```json
{
  "Rules": [
    {
      "Id": "TransitionToGlacier",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 0,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    }
  ]
}
```

**Why others are wrong:**
- **A:** Keeping manual snapshots in Redshift for 7 years is extremely expensive
- **B:** Automated snapshots should stay in Redshift for quick recovery; manual is compliance concern
- **C:** Waiting 1 year to move to S3 wastes 12 months of expensive Redshift storage

**Key Concept:** Operational snapshots in Redshift (7 days); Compliance snapshots → S3 Glacier immediately
</details>

---

## 🟣 SECTION 5: PRACTICE TEST 5 TOPICS

### Question 14: ECS Deployment on AWS Outposts (Similar to PT5-Q8)

A manufacturing company needs to run containerized applications on-premises due to latency requirements (< 5ms) for IoT sensors. The applications must use AWS services and management tools. The company has deployed AWS Outposts in their data center.

Which ECS deployment option should be used?

**A.** ECS on EC2 launch type with Local Zones  
**B.** ECS on EC2 launch type on AWS Outposts  
**C.** ECS on Fargate with Outposts  
**D.** ECS Anywhere with external instances

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**ECS Deployment Options for On-Premises:**

```
AWS Outposts (AWS infrastructure on-premises):
├── Supported: ECS on EC2 ✅
│   ├── EC2 instances run on Outpost rack
│   ├── Control plane in AWS region
│   ├── Data plane (containers) on-premises
│   └── Sub-millisecond latency to local devices
│
├── Not Supported: ECS on Fargate ❌
│   └── Fargate not available on Outposts
│
└── AWS Services Available:
    ├── ECS, EC2, EBS, S3 on Outposts
    └── RDS, ElastiCache (select services)

ECS Anywhere (Non-AWS infrastructure):
├── Supported: Customer-managed servers ✅
│   ├── On-premises servers (not Outposts)
│   ├── Other cloud providers
│   └── Edge locations
│
└── Requires: SSM agent installation

Local Zones (AWS infrastructure in metro areas):
├── Supported: ECS on EC2 ✅
├── Supported: ECS on Fargate ✅
├── Latency: Single-digit milliseconds
└── Use case: Ultra-low latency to users in specific cities
```

**Architecture:**

```
On-Premises Data Center
├── AWS Outposts Rack
│   ├── EC2 Instances (ECS Container Instances)
│   │   ├── ECS Agent
│   │   └── Docker containers running applications
│   ├── EBS Volumes (local storage)
│   └── S3 on Outposts (local object storage)
│
├── IoT Sensors
│   └── < 5ms latency to containers ✅
│
└── Network Connection to AWS Region
    ├── ECS Control Plane (managed in region)
    ├── CloudWatch Logs
    └── ECR (container images)
```

**Why others are wrong:**
- **A:** Local Zones are AWS metro locations, not on-premises; doesn't meet requirement
- **C:** Fargate is not supported on Outposts
- **D:** ECS Anywhere is for non-Outposts on-premises servers; Outposts should use native ECS on EC2

**Key Concept:** Outposts = AWS rack on-premises = ECS on EC2 (not Fargate)
</details>

---

### Question 15: ElastiCache for Multi-AZ High Availability (Similar to PT5-Q26)

A social media application experiences frequent cache misses during peak hours, causing slow database queries. The database team wants to implement a caching layer with automatic failover to a standby cache node in another AZ if the primary fails.

Which ElastiCache configuration provides Multi-AZ automatic failover?

**A.** ElastiCache for Memcached with multiple nodes across AZs  
**B.** ElastiCache for Redis with Cluster Mode disabled and Multi-AZ with automatic failover enabled  
**C.** ElastiCache for Memcached with Auto Discovery enabled  
**D.** ElastiCache for Redis with Cluster Mode enabled (multiple shards)

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**ElastiCache Engines Comparison:**

```
ElastiCache for Memcached:
├── Architecture: Distributed caching
├── Multi-node: Yes (sharding)
├── High Availability: ❌ NO automatic failover
├── Data Persistence: ❌ No (in-memory only)
├── Replication: ❌ No
├── Use case: Simple caching, partition data across nodes
└── Failure: If node fails, data lost; app handles retry

ElastiCache for Redis:
├── Architecture: Master-replica replication
├── Multi-AZ Failover: ✅ YES (automatic)
├── Data Persistence: ✅ YES (snapshots, AOF)
├── Replication: ✅ YES (0-5 read replicas)
├── Use case: Session store, leaderboards, pub/sub
└── Failure: Automatic failover to replica (30-60 seconds)
```

**Redis Multi-AZ Architecture:**

```
AZ-1 (us-east-1a):
├── Primary Node (Master)
│   ├── Accepts writes
│   ├── Replicates to replicas
│   └── IP: 10.0.1.50

AZ-2 (us-east-1b):
├── Replica Node (Read-only)
│   ├── Async replication from primary
│   ├── Promoted to primary on failover ✅
│   └── IP: 10.0.2.50

Failure Scenario:
├── Primary node fails in AZ-1 ❌
├── ElastiCache detects failure (30 seconds)
├── Promotes replica in AZ-2 to primary ✅
├── DNS endpoint updated (automatic)
├── Application connections redirect to new primary
└── Downtime: 30-60 seconds
```

**Configuration (AWS Console):**

```
1. Create ElastiCache for Redis cluster
   ├── Cluster Mode: Disabled
   ├── Node type: cache.r6g.large
   ├── Number of replicas: 1-5
   └── Multi-AZ: Enabled ✅

2. Subnet Group
   ├── Include subnets from multiple AZs
   └── Example: us-east-1a, us-east-1b

3. Automatic Failover
   └── Enabled ✅ (only available with replicas)
```

**Application Connection (Python):**

```python
import redis

# Connect using primary endpoint (automatic failover)
redis_client = redis.Redis(
    host='myredis.abc123.0001.use1.cache.amazonaws.com',  # Primary endpoint
    port=6379,
    decode_responses=True,
    socket_connect_timeout=5,
    socket_timeout=5,
    retry_on_timeout=True
)

# Application doesn't need to handle failover
# ElastiCache updates DNS to new primary automatically
try:
    redis_client.set('user:1001:session', 'abc123', ex=3600)
    session = redis_client.get('user:1001:session')
except redis.ConnectionError:
    print("Connection failed, retry...")
```

**Why others are wrong:**
- **A:** Memcached has no replication or automatic failover
- **C:** Auto Discovery (Memcached) helps find nodes but doesn't provide failover
- **D:** Cluster Mode provides horizontal scaling but question asks specifically for Multi-AZ failover (requires Cluster Mode disabled for traditional Multi-AZ)

**Key Concept:** Redis Multi-AZ = Primary + Replica + Automatic failover; Memcached = No failover
</details>

---

### Question 16: FSx for Lustre with S3 Integration (Similar to PT5-Q17)

A research team processes large genomic datasets (20 TB) stored in S3. They need a high-performance file system for compute-intensive analysis that can:
- Import data from S3 at the start of processing
- Write results back to S3 after computation
- Provide sub-millisecond latency for random access

Which solution meets these requirements cost-effectively?

**A.** FSx for Windows File Server with S3 backup  
**B.** EFS with S3 lifecycle transition  
**C.** FSx for Lustre with S3 data repository integration  
**D.** Instance store volumes with AWS DataSync to S3

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**FSx for Lustre - HPC File System:**

```
FSx for Lustre Features:
├── Performance:
│   ├── Throughput: Up to hundreds of GB/s
│   ├── IOPS: Millions of IOPS
│   ├── Latency: Sub-millisecond
│   └── Use case: HPC, ML training, video processing
│
├── S3 Integration:
│   ├── Link to S3 bucket at creation ✅
│   ├── Lazy load: Read from S3 on first access
│   ├── Data repository tasks: Export to S3
│   └── Bidirectional sync with S3
│
└── Deployment Types:
    ├── Scratch: Temporary, no replication (cheaper)
    └── Persistent: Replicated, long-term storage
```

**Architecture:**

```
Genomic Analysis Workflow:

1. FSx for Lustre Creation:
   ├── Link to S3 bucket: s3://genomic-data/
   ├── Import path: /genomic-data/
   └── Export path: /results/

2. Data Flow:
   S3 Bucket (genomic-data)
      └── 20 TB raw datasets
           │
           │ Lazy load on first access
           ▼
   FSx for Lustre File System
      ├── /genomic-data/ (imported from S3)
      │   └── 20 TB datasets cached locally
      ├── EC2 Compute Cluster
      │   └── Analysis jobs read/write at sub-ms latency ✅
      └── /results/ (processed data)
           │
           │ Export to S3 (data repository task)
           ▼
   S3 Bucket (genomic-results)
      └── Analysis results
```

**S3 Data Repository Configuration:**

```bash
# Create FSx for Lustre with S3 integration
aws fsx create-file-system \
  --file-system-type LUSTRE \
  --storage-capacity 4800 \
  --lustre-configuration '{
    "ImportPath": "s3://genomic-data/raw/",
    "ExportPath": "s3://genomic-results/processed/",
    "DeploymentType": "SCRATCH_2",
    "DataCompressionType": "LZ4"
  }' \
  --subnet-ids subnet-12345678

# Create data repository task (export results to S3)
aws fsx create-data-repository-task \
  --file-system-id fs-0123456789abcdef0 \
  --type EXPORT_TO_REPOSITORY \
  --paths /results/ \
  --report '{
    "Enabled": true,
    "Path": "s3://genomic-results/reports/",
    "Scope": "FAILED_FILES_ONLY"
  }'
```

**Workflow on EC2:**

```bash
# 1. Mount FSx for Lustre
sudo mkdir /mnt/fsx
sudo mount -t lustre fs-0123456789abcdef0.fsx.us-east-1.amazonaws.com@tcp:/fsx /mnt/fsx

# 2. Access data (automatically imports from S3 on first access)
cd /mnt/fsx/genomic-data/
ls -lh sample001.fastq  # Lazy loaded from S3

# 3. Run analysis
./genome-analysis --input /mnt/fsx/genomic-data/ --output /mnt/fsx/results/

# 4. Export results to S3 (manual or scheduled task)
aws fsx create-data-repository-task \
  --file-system-id fs-0123456789abcdef0 \
  --type EXPORT_TO_REPOSITORY \
  --paths /results/
```

**Cost Optimization:**

```
FSx for Lustre Pricing (us-east-1):
├── SCRATCH_2 deployment:
│   ├── $0.140/GB/month (no replication)
│   ├── Example: 4.8 TB × $140/TB = $672/month
│   └── Use case: Temporary processing (delete after)
│
└── PERSISTENT_1 deployment:
    ├── $0.145/GB/month + $0.145/GB/month (replication)
    ├── Example: 4.8 TB × $290/TB = $1,392/month
    └── Use case: Long-term storage with HA

Strategy:
├── Use SCRATCH_2 for temporary analysis
├── Store raw data in S3 Standard ($0.023/GB/month)
├── After processing, export results to S3
└── Delete FSx file system

Cost: $672/month (vs $1,392 for persistent)
```

**Why others are wrong:**
- **A:** FSx for Windows is for Windows workloads; doesn't have S3 integration like Lustre
- **B:** EFS performance insufficient for HPC; no native S3 import/export
- **D:** Instance store is ephemeral; DataSync adds complexity; not purpose-built for HPC

**Key Concept:** FSx for Lustre = HPC file system with native S3 import/export integration
</details>

---

## 🟢 SECTION 6: PRACTICE TEST 6 TOPICS

### Question 17: Site-to-Site VPN Prerequisites (Similar to PT6-Q5)

A company wants to establish a Site-to-Site VPN connection between their on-premises data center and AWS VPC. The architect needs to identify the required components.

Which components are mandatory for establishing the VPN connection? (Choose TWO)

**A.** NAT Gateway in the public subnet  
**B.** Customer Gateway with public IP address  
**C.** Virtual Private Gateway attached to the VPC  
**D.** Transit Gateway for multi-VPC connectivity  
**E.** Internet Gateway for outbound traffic

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answers: B and C**

**Explanation:**

**Site-to-Site VPN Components:**

```
On-Premises Data Center:
├── VPN Device (Firewall/Router)
│   ├── Public IP: 203.0.113.5 ✅
│   ├── Supports IPsec
│   └── BGP capable (for dynamic routing)
│
└── Customer Gateway (AWS resource)
    ├── Logical representation of on-premises device
    ├── Public IP: 203.0.113.5 ✅
    └── BGP ASN: 65000

AWS Cloud:
├── Virtual Private Gateway (VGW) ✅
│   ├── AWS-side VPN endpoint
│   ├── Attached to VPC
│   ├── Two tunnels (redundancy)
│   └── Propagates routes to route tables
│
└── VPC
    ├── Private subnets
    └── Route table: 10.1.0.0/16 → VGW
```

**Minimal Required Configuration:**

```
Step 1: Create Customer Gateway
├── Public IP of on-premises device: 203.0.113.5 ✅
├── Routing: Static or Dynamic (BGP)
└── BGP ASN: 65000 (if dynamic routing)

Step 2: Create Virtual Private Gateway
├── Attach to VPC ✅
└── Type: VPN

Step 3: Create VPN Connection
├── Customer Gateway: cgw-12345
├── Virtual Private Gateway: vgw-67890
├── Routing: Dynamic (BGP) or Static
└── Tunnel options: IKEv2, AES-256

Step 4: Configure On-Premises Device
├── Download configuration file from AWS
├── Apply to firewall/router
└── Establish IPsec tunnels
```

**Route Propagation:**

```
VPC Route Table (after VPN established):
├── Local: 10.0.0.0/16 → local
├── On-premises: 10.1.0.0/16 → vgw-67890 ✅ (propagated)
└── Internet: 0.0.0.0/0 → igw-12345 (if needed)

On-Premises Route Table:
├── Local: 10.1.0.0/16 → local
└── AWS VPC: 10.0.0.0/16 → VPN tunnel ✅
```

**Why others are NOT required:**
- **A (NAT Gateway):** Used for private subnet outbound internet access, NOT for VPN termination
- **D (Transit Gateway):** Alternative to VGW for multi-VPC connectivity, not mandatory
- **E (Internet Gateway):** For VPC internet access, separate from VPN connectivity

**Alternative: Transit Gateway VPN**

```
Modern Architecture (optional):
├── Transit Gateway (instead of VGW)
│   ├── Central hub for multiple VPCs
│   ├── VPN attachment
│   └── Supports ECMP (aggregate bandwidth)
│
└── Use case: Multi-VPC with VPN
```

**Key Concept:** Site-to-Site VPN = Customer Gateway (on-prem public IP) + Virtual Private Gateway (AWS endpoint)
</details>

---

### Question 18: DynamoDB Streams for Change Capture (Similar to PT6-Q35)

An e-commerce application uses DynamoDB to store order data. The development team needs to capture all changes (inserts, updates, deletes) to orders in real-time and trigger downstream processing (inventory updates, notifications).

Which solution captures DynamoDB changes with minimal latency?

**A.** Query DynamoDB with timestamp filter every minute to find new/updated items  
**B.** Enable DynamoDB Streams and process change records with Lambda  
**C.** Export DynamoDB table to S3 every hour and process changes  
**D.** Use CloudWatch Events to monitor DynamoDB API calls

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**DynamoDB Streams - Change Data Capture:**

```
DynamoDB Streams:
├── Captures: INSERT, MODIFY, DELETE operations
├── Latency: Near real-time (milliseconds)
├── Retention: 24 hours
├── Ordering: Per partition key (FIFO)
└── Consumers: Lambda, Kinesis Data Streams, custom applications
```

**Architecture:**

```
DynamoDB Table (Orders):
├── PutItem (new order) → Stream record
├── UpdateItem (status change) → Stream record
└── DeleteItem (cancel order) → Stream record
        │
        │ Real-time stream
        ▼
DynamoDB Stream:
├── Record 1: INSERT order-001 {"total": 99.99, "status": "pending"}
├── Record 2: MODIFY order-001 {"status": "confirmed"}
└── Record 3: DELETE order-002
        │
        │ Trigger Lambda
        ▼
Lambda Function (Process Changes):
├── Receive batch of stream records
├── Process each record:
│   ├── INSERT → Send confirmation email
│   ├── MODIFY → Update inventory
│   └── DELETE → Refund payment
└── Write to downstream systems
```

**Stream Record Structure:**

```json
{
  "Records": [
    {
      "eventID": "1",
      "eventName": "INSERT",
      "eventVersion": "1.1",
      "eventSource": "aws:dynamodb",
      "awsRegion": "us-east-1",
      "dynamodb": {
        "Keys": {
          "OrderId": {"S": "order-001"}
        },
        "NewImage": {
          "OrderId": {"S": "order-001"},
          "CustomerId": {"S": "cust-123"},
          "Total": {"N": "99.99"},
          "Status": {"S": "pending"},
          "Timestamp": {"N": "1678300800"}
        },
        "SequenceNumber": "111",
        "SizeBytes": 128,
        "StreamViewType": "NEW_AND_OLD_IMAGES"
      }
    }
  ]
}
```

**Stream View Types:**

| View Type | OLD_IMAGE | NEW_IMAGE | Use Case |
|-----------|-----------|-----------|----------|
| **KEYS_ONLY** | ❌ | ❌ | Minimal data; trigger only |
| **NEW_IMAGE** | ❌ | ✅ | See new values after change |
| **OLD_IMAGE** | ✅ | ❌ | See values before change |
| **NEW_AND_OLD_IMAGES** | ✅ | ✅ | Compare before/after (recommended) |

**Implementation:**

**1. Enable DynamoDB Stream:**

```bash
aws dynamodb update-table \
  --table-name Orders \
  --stream-specification \
    StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES
```

**2. Create Lambda Function:**

```python
import json
import boto3

def lambda_handler(event, context):
    for record in event['Records']:
        event_name = record['eventName']  # INSERT, MODIFY, or REMOVE
        
        if event_name == 'INSERT':
            new_order = record['dynamodb']['NewImage']
            send_confirmation_email(new_order)
            
        elif event_name == 'MODIFY':
            old_status = record['dynamodb']['OldImage'].get('Status', {}).get('S')
            new_status = record['dynamodb']['NewImage'].get('Status', {}).get('S')
            
            if old_status == 'pending' and new_status == 'confirmed':
                update_inventory(new_order)
                
        elif event_name == 'REMOVE':
            old_order = record['dynamodb']['OldImage']
            process_refund(old_order)
    
    return {'statusCode': 200}
```

**3. Add Lambda Event Source Mapping:**

```bash
aws lambda create-event-source-mapping \
  --function-name ProcessOrders \
  --event-source-arn arn:aws:dynamodb:us-east-1:123456789012:table/Orders/stream/2024-03-06T00:00:00.000 \
  --starting-position LATEST \
  --batch-size 100 \
  --maximum-batching-window-in-seconds 10
```

**Performance Characteristics:**

```
Latency:
├── DynamoDB write → Stream: < 1 second
├── Stream → Lambda trigger: < 1 second
└── Total: 1-2 seconds (near real-time) ✅

Throughput:
├── Shard capacity: 1,000 records/second
├── Lambda batching: Up to 10,000 records per invocation
└── Scales automatically with table partitions
```

**Why others are wrong:**
- **A:** Polling with timestamp queries is inefficient, high latency (1 minute), expensive reads
- **C:** Hourly export has 1-hour latency, not real-time
- **D:** CloudWatch Events tracks API calls but doesn't capture actual data changes

**Key Concept:** DynamoDB Streams = Real-time change capture + Lambda processing pattern
</details>

---

## 🔴 SECTION 7: PRACTICE TEST 7 TOPICS

### Question 19: Direct Connect BGP Community Tags (Similar to PT7-Q1)

A company has a Direct Connect connection with a public virtual interface to access AWS public services (S3, DynamoDB). To optimize routing and reduce data transfer costs, the network team wants to ensure that traffic to S3 in us-east-1 only uses AWS infrastructure within the us-east-1 region and does NOT traverse to other regions.

Which BGP configuration should be applied?

**A.** Configure NO_EXPORT BGP community tag  
**B.** Configure local preference BGP community tag with value 200  
**C.** Configure BGP community tag 7224:9100 (local region preference)  
**D.** Configure BGP community tag 7224:9300 (global preference)

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**Direct Connect BGP Community Tags:**

```
AWS BGP Community Tags (Public VIF only):
├── 7224:9100 → Local AWS region only ✅
│   └── Use case: Keep traffic in same region as Direct Connect
│
├── 7224:9200 → All regions in same continent
│   └── Use case: US regions, or EU regions, etc.
│
├── 7224:9300 → Global (all AWS regions) [default]
│   └── Use case: Access any AWS region
│
└── NO_EXPORT → Don't advertise beyond AS
    └── Use case: Prevent route propagation
```

**Scenario Architecture:**

```
On-Premises Data Center (Virginia):
├── Direct Connect Location (us-east-1)
│   └── Public Virtual Interface
│       ├── BGP community: 7224:9100 ✅
│       └── VLAN: 100
│
└── Traffic Flow with 7224:9100:
    ├── S3 us-east-1: Uses local region ✅ (lowest latency)
    ├── S3 us-west-2: ❌ NOT advertised (must use internet)
    └── DynamoDB us-east-1: Uses local region ✅

Without 7224:9100 (default 7224:9300):
    ├── S3 us-east-1: Uses local region
    ├── S3 us-west-2: Uses Direct Connect → Cross-region transfer $$
    └── May route through longer path
```

**BGP Configuration (Cisco Example):**

```cisco
router bgp 65000
 neighbor 169.254.0.1 remote-as 7224
 neighbor 169.254.0.1 description AWS-Direct-Connect
 
 address-family ipv4
  neighbor 169.254.0.1 activate
  neighbor 169.254.0.1 soft-reconfiguration inbound
  
  ! Apply community tag 7224:9100 to routes advertised to AWS
  neighbor 169.254.0.1 send-community
  neighbor 169.254.0.1 route-map SET-AWS-LOCAL out
 exit-address-family

route-map SET-AWS-LOCAL permit 10
 set community 7224:9100    ! Local region only
```

**Cost Impact:**

```
Scenario 1: Without 7224:9100 (default global)
├── Direct Connect to S3 us-east-1: $0.02/GB (Data Transfer Out)
├── Direct Connect to S3 us-west-2:
│   ├── Data Transfer Out: $0.02/GB
│   ├── Cross-region transfer: $0.02/GB
│   └── Total: $0.04/GB ❌ (expensive)

Scenario 2: With 7224:9100 (local region)
├── Direct Connect to S3 us-east-1: $0.02/GB ✅
├── Direct Connect to S3 us-west-2: Uses internet instead
│   └── May use VPN or public internet (application decides)
```

**Virtual Interface Types:**

| VIF Type | Supports BGP Community Tags | Use Case |
|----------|---------------------------|----------|
| **Public VIF** | 7224:9100, 7224:9200, 7224:9300, NO_EXPORT ✅ | AWS public services (S3, DynamoDB) |
| **Private VIF** | Local preference (NOT 7224:xxxx) | VPC resources |
| **Transit VIF** | Local preference (NOT 7224:xxxx) | Transit Gateway |

**Why others are wrong:**
- **A:** NO_EXPORT prevents propagation but doesn't ensure local region routing
- **B:** Local preference BGP communities are for private/transit VIFs, NOT public VIFs
- **D:** 7224:9300 is global (default), allows cross-region routing (higher cost)

**Key Concept:** Public VIF + 7224:9100 = Local region routing only (cost optimization)
</details>

---

### Question 20: AWS DataSync for EFS Cross-Region Transfer (Similar to PT7-Q4)

A company is migrating an application from us-west-1 to eu-west-2. The application uses Amazon EFS with 50 TB of data. The architect needs a solution to transfer all EFS data to a new EFS file system in eu-west-2 with minimal operational overhead.

Which solution is most appropriate?

**A.** Use AWS Snowball Edge to export EFS data, ship to eu-west-2, import to new EFS  
**B.** Set up S3 Cross-Region Replication as intermediate storage  
**C.** Use AWS DataSync to transfer directly from source EFS to destination EFS  
**D.** Write custom script using rsync over VPN between regions

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**AWS DataSync - Purpose-Built Data Transfer:**

```
AWS DataSync Features:
├── Supports:
│   ├── EFS → EFS (same/cross-region) ✅
│   ├── EFS → S3
│   ├── S3 → EFS
│   ├── On-premises → AWS
│   └── FSx → FSx
│
├── Benefits:
│   ├── Automated transfer (no scripting)
│   ├── Incremental transfers
│   ├── Data integrity validation
│   ├── Encryption in transit (TLS)
│   ├── Bandwidth throttling
│   └── Scheduling capabilities
│
└── Performance:
    └── Up to 10 Gbps per task
```

**Architecture:**

```
Source Region (us-west-1):
├── EFS File System (fs-abc123)
│   └── 50 TB data
│
└── DataSync Task
    ├── Source Location: EFS fs-abc123
    ├── Destination Location: EFS fs-xyz789 (eu-west-2)
    ├── Transfer over AWS private network ✅
    └── No internet traversal

Destination Region (eu-west-2):
├── EFS File System (fs-xyz789)
│   └── Receives 50 TB data
│
└── DataSync Agent (not required for EFS-to-EFS)
```

**Implementation:**

**Step 1: Create Source Location**

```bash
aws datasync create-location-efs \
  --efs-filesystem-arn arn:aws:elasticfilesystem:us-west-1:123456789012:file-system/fs-abc123 \
  --ec2-config SubnetArn=arn:aws:ec2:us-west-1:123456789012:subnet/subnet-12345678,SecurityGroupArns=arn:aws:ec2:us-west-1:123456789012:security-group/sg-12345678 \
  --region us-west-1
```

**Step 2: Create Destination Location**

```bash
aws datasync create-location-efs \
  --efs-filesystem-arn arn:aws:elasticfilesystem:eu-west-2:123456789012:file-system/fs-xyz789 \
  --ec2-config SubnetArn=arn:aws:ec2:eu-west-2:123456789012:subnet/subnet-87654321,SecurityGroupArns=arn:aws:ec2:eu-west-2:123456789012:security-group/sg-87654321 \
  --region eu-west-2
```

**Step 3: Create DataSync Task**

```bash
aws datasync create-task \
  --source-location-arn arn:aws:datasync:us-west-1:123456789012:location/loc-source123 \
  --destination-location-arn arn:aws:datasync:eu-west-2:123456789012:location/loc-dest456 \
  --name "EFS-Migration-Task" \
  --options '{
    "VerifyMode": "POINT_IN_TIME_CONSISTENT",
    "OverwriteMode": "ALWAYS",
    "TransferMode": "CHANGED",
    "PreserveDeletedFiles": "REMOVE"
  }' \
  --region us-west-1
```

**Step 4: Execute Task**

```bash
aws datasync start-task-execution \
  --task-arn arn:aws:datasync:us-west-1:123456789012:task/task-123abc
```

**Transfer Performance:**

```
50 TB Transfer Estimation:
├── Bandwidth: 10 Gbps (DataSync max per task)
├── Effective throughput: ~1 GB/s (8 Gbps realistic)
├── Time: 50,000 GB ÷ 1 GB/s = ~13.9 hours
└── Cost: Data Transfer Out us-west-1: $0.02/GB × 50,000 GB = $1,000
```

**DataSync Task Options:**

```yaml
Options:
  VerifyMode: POINT_IN_TIME_CONSISTENT
    # Validates data integrity
  
  OverwriteMode: ALWAYS
    # Overwrites files if exists in destination
  
  TransferMode: CHANGED
    # Only transfers modified files (incremental)
  
  PreserveDeletedFiles: REMOVE
    # Deletes files in destination if deleted in source
  
  TaskQueueing: ENABLED
    # Queue tasks if another is running
  
  LogLevel: TRANSFER
    # Logging detail level
```

**Monitoring:**

```bash
# Check task execution status
aws datasync describe-task-execution \
  --task-execution-arn arn:aws:datasync:us-west-1:123456789012:task/task-123abc/execution/exec-456def

# Output includes:
# - FilesTransferred
# - BytesTransferred
# - Status (LAUNCHING, PREPARING, TRANSFERRING, VERIFYING, SUCCESS)
# - Estimated time remaining
```

**Why others are wrong:**
- **A:** Snowball Edge is for offline transfer; 50 TB can transfer online in ~14 hours; Snowball adds 1-2 weeks shipping time
- **B:** S3 CRR requires exporting EFS to S3, replication, then importing to EFS (3 steps vs 1)
- **D:** rsync is manual, requires scripting, error handling, monitoring; DataSync is purpose-built

**Key Concept:** DataSync = Automated, validated, encrypted EFS-to-EFS cross-region transfer
</details>

---

### Question 21: NAT Gateway Cost Optimization (Similar to PT7-Q16)

A company has a VPC with 3 Availability Zones. Private subnets in each AZ have EC2 instances that need internet access for software updates. Currently, all private subnets route to a single NAT Gateway in us-east-1a. The CFO has noticed high data transfer costs.

Which solution reduces data transfer costs while maintaining internet access?

**A.** Replace NAT Gateway with NAT Instance  
**B.** Deploy a public NAT Gateway in each AZ; route each AZ's private subnets to the NAT Gateway in the same AZ  
**C.** Deploy VPC endpoints for all AWS services  
**D.** Deploy a private NAT Gateway in each AZ's public subnet

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**Current Architecture (Cost Issue):**

```
VPC (10.0.0.0/16):
├── AZ-A (us-east-1a):
│   ├── Public Subnet
│   │   └── NAT Gateway (nat-abc123) ✅
│   └── Private Subnet
│       └── EC2 instances → NAT in AZ-A (same AZ) ✅
│
├── AZ-B (us-east-1b):
│   ├── Public Subnet (no NAT Gateway)
│   └── Private Subnet
│       └── EC2 instances → NAT in AZ-A ❌ (cross-AZ traffic)
│
└── AZ-C (us-east-1c):
    ├── Public Subnet (no NAT Gateway)
    └── Private Subnet
        └── EC2 instances → NAT in AZ-A ❌ (cross-AZ traffic)

Cost Issue:
└── Cross-AZ data transfer: $0.01/GB (AZ-B → AZ-A, AZ-C → AZ-A)
```

**Optimized Architecture:**

```
VPC (10.0.0.0/16):
├── AZ-A (us-east-1a):
│   ├── Public Subnet
│   │   └── NAT Gateway-A (nat-123)
│   └── Private Subnet
│       ├── EC2 instances
│       └── Route: 0.0.0.0/0 → nat-123 ✅ (same AZ)
│
├── AZ-B (us-east-1b):
│   ├── Public Subnet
│   │   └── NAT Gateway-B (nat-456) ✅ (added)
│   └── Private Subnet
│       ├── EC2 instances
│       └── Route: 0.0.0.0/0 → nat-456 ✅ (same AZ)
│
└── AZ-C (us-east-1c):
    ├── Public Subnet
    │   └── NAT Gateway-C (nat-789) ✅ (added)
    └── Private Subnet
        ├── EC2 instances
        └── Route: 0.0.0.0/0 → nat-789 ✅ (same AZ)

Cost Savings:
└── Eliminated cross-AZ data transfer charges
```

**Route Table Configuration:**

```
Private Subnet Route Table (AZ-A):
├── 10.0.0.0/16 → local
└── 0.0.0.0/0 → nat-123 (NAT in AZ-A) ✅

Private Subnet Route Table (AZ-B):
├── 10.0.0.0/16 → local
└── 0.0.0.0/0 → nat-456 (NAT in AZ-B) ✅

Private Subnet Route Table (AZ-C):
├── 10.0.0.0/16 → local
└── 0.0.0.0/0 → nat-789 (NAT in AZ-C) ✅
```

**Cost Analysis:**

```
Assumptions:
├── 3 AZs, 10 EC2 instances per AZ (30 total)
├── Each instance: 100 GB/month outbound internet traffic
├── Total: 3,000 GB/month

Scenario 1: Single NAT Gateway (Current)
├── NAT Gateway charges:
│   ├── Hourly: $0.045/hr × 730 hrs = $32.85/month
│   └── Data processing: $0.045/GB × 3,000 GB = $135/month
├── Cross-AZ data transfer:
│   ├── AZ-A: 1,000 GB (no cross-AZ charge)
│   ├── AZ-B: 1,000 GB × $0.01/GB = $10 (cross-AZ) ❌
│   └── AZ-C: 1,000 GB × $0.01/GB = $10 (cross-AZ) ❌
└── Total: $187.85/month

Scenario 2: NAT Gateway per AZ (Optimized)
├── NAT Gateway charges:
│   ├── 3 gateways × $0.045/hr × 730 hrs = $98.55/month
│   └── Data processing: $0.045/GB × 3,000 GB = $135/month
├── Cross-AZ data transfer:
│   └── $0 (all traffic stays in same AZ) ✅
└── Total: $233.55/month

Wait, this is MORE expensive!
```

**When Does Per-AZ NAT Gateway Save Money?**

```
Break-even analysis:
├── Additional NAT cost: 2 × $32.85 = $65.70/month
├── Cross-AZ savings: $0.01/GB
└── Need: $65.70 ÷ $0.01 = 6,570 GB/month cross-AZ traffic

If cross-AZ traffic > 6,570 GB/month → Per-AZ NAT is cheaper ✅
If cross-AZ traffic < 6,570 GB/month → Single NAT is cheaper

High-traffic scenario (100 instances, 1 TB/month each):
├── Single NAT: $32.85 + $4,500 + $666 (cross-AZ) = $5,198.85
├── Per-AZ NAT: $98.55 + $4,500 = $4,598.55 ✅
└── Savings: $600/month
```

**NAT Gateway Types:**

```
Public NAT Gateway: ✅
├── Deployed in public subnet
├── Requires Elastic IP
├── Routes: 0.0.0.0/0 → Internet Gateway
└── Use case: Private EC2 → Internet access

Private NAT Gateway:
├── Deployed in private subnet
├── No Elastic IP
├── Routes to Transit Gateway, VPN, Direct Connect
└── Use case: VPC-to-VPC, on-premises connectivity (NOT internet)
```

**Why others are wrong:**
- **A:** NAT Instance requires management (patching, scaling); still has cross-AZ charges
- **C:** VPC endpoints only work for AWS services, not general internet access
- **D:** Private NAT Gateway is for VPC/on-premises connectivity, NOT internet access

**Key Concept:** High cross-AZ traffic → NAT Gateway per AZ eliminates $0.01/GB cross-AZ charges
</details>

---

### Question 22: Redshift Cluster Encryption at Rest (Similar to PT7-Q35)

A data analytics team has an existing Amazon Redshift cluster (5 TB) with encryption disabled. Due to new compliance requirements, the security team requires encryption at rest using AWS KMS. The solution should minimize downtime.

Which solution enables encryption on the existing cluster?

**A.** Modify the cluster properties to enable encryption using AWS KMS; Redshift automatically migrates data  
**B.** Create unload scripts to export data, create new encrypted cluster, reload data  
**C.** Enable encryption using CloudHSM instead of KMS  
**D.** Use AWS Database Migration Service to migrate to new encrypted cluster

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: A**

**Explanation:**

**Redshift Encryption Changes (2019+):**

```
Legacy Approach (Pre-2019): ❌
├── Cannot enable encryption on existing cluster
├── Required steps:
│   ├── 1. Create new encrypted cluster
│   ├── 2. Unload data from old cluster
│   ├── 3. Load data into new cluster
│   ├── 4. Switch application endpoints
│   └── 5. Delete old cluster
└── Downtime: Several hours for 5 TB

Modern Approach (2019+): ✅
├── Enable encryption on existing cluster ✅
├── Redshift automatically migrates data
├── Process:
│   ├── 1. Modify cluster → Enable encryption
│   ├── 2. Select KMS key
│   ├── 3. Redshift creates encrypted snapshots
│   ├── 4. Redshift migrates data (background)
│   └── 5. Cluster remains available (read-only during final cutover)
└── Downtime: Minimal (minutes for final cutover)
```

**Implementation:**

**AWS Console:**

```
1. Navigate to Redshift Console
2. Select cluster: my-cluster
3. Actions → Modify cluster
4. Database configuration:
   ├── Encryption: Enable ✅
   ├── KMS key: aws/redshift (default) or custom key
   └── Click "Modify cluster"
5. Redshift migration process:
   ├── Status: "Modifying"
   ├── Progress: 0% → 100%
   ├── Duration: ~2-4 hours for 5 TB
   └── Final status: "Available" (encrypted)
```

**AWS CLI:**

```bash
# Modify existing cluster to enable encryption
aws redshift modify-cluster \
  --cluster-identifier my-cluster \
  --encrypted \
  --kms-key-id arn:aws:kms:us-east-1:123456789012:key/abc12345-6789-0def-gh12-3456789abcde

# Check encryption status
aws redshift describe-clusters \
  --cluster-identifier my-cluster \
  --query 'Clusters[0].[Encrypted,KmsKeyId]' \
  --output table
```

**Migration Process:**

```
Phase 1: Snapshot Creation
├── Redshift creates encrypted snapshot
├── Uses specified KMS key
└── Duration: ~30 minutes for 5 TB

Phase 2: Data Migration (Background)
├── Redshift copies data to encrypted storage
├── Cluster remains AVAILABLE for queries ✅
├── Performance may be slightly reduced
└── Duration: 2-4 hours for 5 TB

Phase 3: Final Cutover
├── Brief read-only period (5-10 minutes)
├── Switches to encrypted storage
└── Cluster returns to AVAILABLE status

Total Downtime: ~10 minutes (minimal) ✅
```

**Encryption Options:**

```
AWS KMS (Recommended): ✅
├── AWS-managed key: aws/redshift (default)
├── Customer-managed key: Custom KMS key
├── Benefits:
│   ├── Automated key rotation
│   ├── CloudTrail integration
│   ├── Fine-grained access control
│   └── Multi-tenant (AWS-managed infrastructure)
└── Cost: $1/key/month + API requests

AWS CloudHSM:
├── Single-tenant hardware security module
├── Benefits:
│   ├── FIPS 140-2 Level 3 compliance
│   ├── Customer manages keys
│   └── High security requirements
└── Cost: $1.60/hour per HSM (~$1,168/month) + setup
```

**What Gets Encrypted:**

```
Redshift Encrypted Cluster:
├── Cluster data blocks ✅
├── System tables ✅
├── All snapshots (automated & manual) ✅
├── Backups ✅
└── In-transit data between nodes ✅

NOT Encrypted (Without additional configuration):
├── Connection to cluster (use SSL) ⚠️
└── Query results downloaded to client ⚠️
```

**Verification:**

```sql
-- Check encryption status from Redshift query editor
SELECT
  current_database() as database,
  encrypted
FROM pg_database_info
WHERE datname = current_database();

-- Output:
--  database | encrypted
-- ----------+-----------
--  dev      | true
```

**Why others are wrong:**
- **B:** Manual unload/reload was required pre-2019; no longer necessary
- **C:** CloudHSM is more expensive and complex; KMS is sufficient for most compliance needs
- **D:** DMS adds unnecessary complexity; native Redshift feature is simpler

**Key Concept:** Modern Redshift (2019+) = Enable encryption on existing cluster with minimal downtime
</details>

---

## 📊 Progress Tracker

Mark questions you got wrong for review:

**Test 1 Topics:**
- [ ] Q1: ECS Task Definition
- [ ] Q2: Route 53 Failover
- [ ] Q3: VPC Endpoints
- [ ] Q4: CloudFormation Cross-Stack
- [ ] Q5: Transit Gateway ECMP

**Test 2 Topics:**
- [ ] Q6: CloudFront ACM us-east-1
- [ ] Q7: ALB Health Checks
- [ ] Q8: GuardDuty

**Test 3 Topics:**
- [ ] Q9: Glacier Retrieval
- [ ] Q10: S3 Multipart Upload

**Test 4 Topics:**
- [ ] Q11: CloudWatch Custom Metrics
- [ ] Q12: Savings Plans
- [ ] Q13: Redshift Snapshots

**Test 5 Topics:**
- [ ] Q14: ECS on Outposts
- [ ] Q15: ElastiCache Multi-AZ
- [ ] Q16: FSx for Lustre

**Test 6 Topics:**
- [ ] Q17: Site-to-Site VPN
- [ ] Q18: DynamoDB Streams

**Test 7 Topics:**
- [ ] Q19: Direct Connect BGP
- [ ] Q20: DataSync EFS
- [ ] Q21: NAT Gateway Cost
- [ ] Q22: Redshift Encryption

---

## 🎯 Next Steps

1. **First Pass:** Answer all 22 questions WITHOUT looking at answers
2. **Score yourself:** Calculate percentage correct
3. **Review wrong answers:** Study explanations thoroughly
4. **Create flashcards:** For concepts you missed
5. **Retry in 24 hours:** Test retention
6. **Retry in 1 week:** Ensure long-term retention
7. **Target:** 90%+ correct on second attempt

---

## 🟤 ADDITIONAL REINFORCEMENT QUESTIONS

### Question 23: RDS Multi-AZ vs Read Replicas (Similar to PT1)

A financial services company has an RDS PostgreSQL database experiencing high read traffic during business hours. The development team wants to scale read capacity to improve application performance. The database also requires high availability with automatic failover.

Which solution provides both read scaling AND high availability?

**A.** Enable Multi-AZ deployment only  
**B.** Create Read Replicas in multiple AZs only  
**C.** Enable Multi-AZ deployment and create Read Replicas  
**D.** Use Amazon Aurora with single instance

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**RDS Multi-AZ vs Read Replicas:**

```
Multi-AZ Deployment:
├── Purpose: High availability (HA) only ✅
├── Standby: NOT accessible for reads ❌
└── Use case: HA/DR, not performance scaling

Read Replicas:
├── Purpose: Read scaling ✅
├── Replicas: Accessible for read queries ✅
└── Use case: Scale read-heavy workloads

Combined Solution: Multi-AZ + Read Replicas ✅
├── Multi-AZ: Primary + Standby (HA)
├── Read Replicas: 3 replicas for read scaling
└── Benefits: HA + Read scaling
```

**Key Concept:** Multi-AZ = HA (standby not readable); Read Replicas = Scaling (readable)
</details>

---

### Question 24: Lambda Execution Role CloudWatch Logs (Similar to PT5-Q55)

A Lambda function executes successfully but no logs appear in CloudWatch Logs. What is the most likely cause?

**A.** Lambda function timeout is too short  
**B.** Lambda execution role lacks CloudWatch Logs permissions  
**C.** CloudWatch Logs retention period expired  
**D.** Lambda function not using console.log() statements

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**Required CloudWatch Logs Permissions:**

```json
{
  "Effect": "Allow",
  "Action": [
    "logs:CreateLogGroup",
    "logs:CreateLogStream",
    "logs:PutLogEvents"
  ],
  "Resource": "arn:aws:logs:*:*:*"
}
```

**Common Mistake:**
- Lambda executes successfully
- BUT no logs appear due to missing permissions
- Solution: Attach AWSLambdaBasicExecutionRole or equivalent

**Key Concept:** All Lambda functions need CloudWatch Logs permissions for debugging
</details>

---

### Question 25: Auto Scaling Cooldown Period (Similar to PT5-Q21)

An Auto Scaling group scales out from 4 to 6 instances at 10:00 AM due to high CPU. At 10:02 AM, CPU is still high but no additional instances launch. The cooldown period is 300 seconds. Why?

**A.** New instances are still initializing  
**B.** Cooldown period prevents scaling for 5 minutes after previous scale  
**C.** Auto Scaling group reached maximum capacity  
**D.** Target tracking policies don't support multiple scale events

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**Auto Scaling Cooldown:**

```
10:00 AM: Scale out (4 → 6 instances)
├── Cooldown starts: 300 seconds ⏱️
├── During cooldown: No additional scaling ❌
└── After 5 minutes: Resume normal evaluation

10:02 AM: Still high CPU
└── No action: Still in cooldown (120/300 seconds)

10:05 AM: Cooldown expires
└── Resume scaling if still needed ✅
```

**Key Concept:** Cooldown prevents rapid successive scaling to allow metric stabilization
</details>

---

### Question 26: CloudFront Custom Error Response (Similar to PT5-Q24)

A website uses CloudFront with S3 origin. When files are missing (404), display custom error page `/error.html` with HTTP 200 status. How to configure?

**A.** S3 bucket error document configuration  
**B.** CloudFront custom error response: 404 → /error.html, status 200  
**C.** Lambda@Edge to intercept 404 errors  
**D.** CloudFront default error pages

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**CloudFront Custom Error Response:**

```
Configuration:
├── Error Code: 404
├── Response Page: /error.html
├── Response Code: 200 (not 404) ✅
└── Cache TTL: 300 seconds
```

**Flow:**
1. Client requests missing file
2. Origin returns 404
3. CloudFront fetches /error.html
4. Returns to client as 200 OK

**Key Concept:** CloudFront custom error response allows branded error pages with custom status codes
</details>

---

### Question 27: EBS Provisioned IOPS for Database (Similar to PT6-Q6)

Database requires 20,000 IOPS consistent performance. Which EBS volume type?

**A.** gp3 with 20,000 provisioned IOPS  
**B.** io2 with 20,000 provisioned IOPS  
**C.** gp2 with large volume for 20,000 baseline  
**D.** st1 for high throughput

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**EBS Volume Limits:**

```
gp3: Up to 16,000 IOPS max ❌
├── Cannot provision 20,000 IOPS
└── Need io2 instead

io2: Up to 64,000 IOPS ✅
├── Can provision 20,000 IOPS
├── Sub-millisecond latency
└── Cost: Higher than gp3
```

**Decision:**
- Need > 16,000 IOPS → Use io2
- Need ≤ 16,000 IOPS → Use gp3 (cheaper)

**Key Concept:** gp3 max = 16,000 IOPS; io2 for higher IOPS requirements
</details>

---

### Question 28: Site-to-Site VPN Required Components (Similar to PT6-Q5)

To establish Site-to-Site VPN between on-premises and AWS VPC, which components are mandatory? (Choose TWO)

**A.** NAT Gateway  
**B.** Customer Gateway with public IP  
**C.** Virtual Private Gateway attached to VPC  
**D.** Internet Gateway  
**E.** Transit Gateway

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answers: B and C**

**Explanation:**

**Required VPN Components:**

```
On-Premises:
└── Customer Gateway (public IP) ✅

AWS:
└── Virtual Private Gateway (attached to VPC) ✅

Optional:
├── Transit Gateway (alternative to VGW)
├── NAT Gateway (for outbound internet, not VPN)
└── IGW (for general internet, not VPN)
```

**Key Concept:** VPN = Customer Gateway (public IP) + Virtual Private Gateway (AWS endpoint)
</details>

---

### Question 29: DynamoDB Streams Change Capture (Similar to PT6-Q35)

Capture real-time changes (inserts, updates, deletes) from DynamoDB table. Which solution?

**A.** Query table every minute with timestamp filter  
**B.** Enable DynamoDB Streams, process with Lambda  
**C.** Export table to S3 hourly  
**D.** CloudWatch Events for DynamoDB API calls

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: B**

**Explanation:**

**DynamoDB Streams:**

```
Features:
├── Captures: INSERT, MODIFY, DELETE
├── Latency: Milliseconds (near real-time) ✅
├── Retention: 24 hours
└── Consumer: Lambda, Kinesis, custom apps

Flow:
DynamoDB Table → Stream → Lambda → Process
```

**Why others wrong:**
- A: Polling is inefficient, 1-minute latency
- C: Hourly export = 1-hour latency
- D: CloudWatch Events tracks API calls, not data changes

**Key Concept:** DynamoDB Streams = Real-time change data capture
</details>

---

### Question 30: Direct Connect BGP Community Tags (Similar to PT7-Q1)

Direct Connect public VIF to S3. Traffic should stay in local region only (us-east-1). Which BGP tag?

**A.** NO_EXPORT  
**B.** Local preference 200  
**C.** BGP community 7224:9100 (local region)  
**D.** BGP community 7224:9300 (global)

<details>
<summary>✅ Click to reveal answer</summary>

**Correct Answer: C**

**Explanation:**

**AWS BGP Community Tags (Public VIF):**

```
7224:9100 → Local region only ✅
├── Traffic stays in same region
└── Use case: Cost optimization

7224:9200 → Continental (all regions in continent)
7224:9300 → Global (all regions) [default]
NO_EXPORT → Don't advertise beyond AS
```

**Cost Impact:**
- Without tag: May route cross-region ($0.02/GB extra)
- With 7224:9100: Stays local (saves money) ✅

**Key Concept:** Public VIF + 7224:9100 = Local region routing (cost savings)
</details>

---

## 📚 Additional Study Resources

After completing these questions, review:
- AWS Documentation for services you got wrong
- AWS Well-Architected Framework
- Hands-on labs for weak areas
- Original practice tests (retake after 2 weeks)

**Total Questions Created:** 30 comprehensive reinforcement questions covering all 7 practice tests

**Good luck with your AWS SAA-C03 exam preparation!** 🚀

