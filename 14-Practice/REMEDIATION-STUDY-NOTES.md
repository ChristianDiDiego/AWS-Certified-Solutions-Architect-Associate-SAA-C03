# 📚 Remediation Study Notes - Practice Test 3
## Focus Areas for Improvement

Based on your quiz results (52/65 - 80%), here are targeted study notes for the areas where you had incorrect answers.

---

## 🎯 1. S3 Storage Classes & Glacier Operations

### ❌ **What You Missed:**
- **Question 4**: S3 Glacier Deep Archive restoration process
- **Question 7**: Choosing appropriate S3 storage for frequently accessed data

### ✅ **Key Concepts:**

#### S3 Glacier Deep Archive Restoration Process
```
Objects in Glacier Deep Archive CANNOT be directly copied or moved.
Must follow this process:
1. Initiate a RESTORE request (creates temporary copy in S3 Standard)
2. Wait for restore to complete (12-48 hours for Deep Archive)
3. During restore window: Copy object with new storage class
4. Delete original archived object
```

#### S3 Storage Class Selection Guide
| Use Case | Storage Class | Why |
|----------|---------------|-----|
| Frequently accessed data | **S3 Standard** | Low latency, high throughput |
| Infrequent access (>30 days) | S3 Standard-IA | Lower cost, retrieval fee |
| Archive (rarely accessed) | S3 Glacier | Very low cost, slow retrieval |
| Long-term archive | Glacier Deep Archive | Lowest cost, 12+ hr retrieval |

**🔑 Remember:** For transcription platforms with frequent access needs:
- Use **S3 Standard** for active data
- NOT Glacier Vault (that's for archival, not frequent access)
- EBS/Instance Store are NOT suitable for shared, durable storage

---

## 🔐 2. AWS Migration Services (DMS, DataSync, Migration Hub)

### ❌ **What You Missed:**
**Question 9**: SQL Server to MySQL migration with schema conversion

### ✅ **Key Concepts:**

#### AWS Database Migration Service (DMS) Architecture
```
Heterogeneous Migration: SQL Server → MySQL
├── Step 1: Schema Conversion Tool (SCT)
│   └── Converts schema & code objects
├── Step 2: AWS DMS
│   ├── Full load migration
│   └── Continuous data replication (CDC)
└── Step 3: AWS Migration Hub
    └── Centralized tracking & monitoring
```

#### When to Use Each Service
| Service | Purpose | Use When |
|---------|---------|----------|
| **AWS DMS** | Database migration | Migrating databases with minimal downtime |
| **Schema Conversion Tool (SCT)** | Schema conversion | Converting between different DB engines |
| **AWS Migration Hub** | Progress tracking | Need visibility across multiple migrations |
| **AWS DataSync** | File transfer | Migrating file systems, NOT databases |
| **Application Migration Service (MGN)** | Server rehosting | Lift-and-shift VM migrations |

**🔑 Remember:** 
- DMS handles the DATA migration
- SCT handles the SCHEMA conversion
- Migration Hub provides the DASHBOARD
- These work TOGETHER for heterogeneous migrations

---

## 🛡️ 3. AWS WAF & Application Security

### ❌ **What You Missed:**
**Question 14**: Protecting PHP application from vulnerabilities

### ✅ **Key Concepts:**

#### AWS WAF Managed Rule Groups
```
AWS WAF Web ACL
├── Core Rule Set (CRS)
├── Known Bad Inputs
├── SQL Injection
├── **AWS-AWSManagedRulesPHPRuleSet** ← Blocks PHP-specific attacks
├── Linux Operating System
└── Windows Operating System
```

#### AWS Shield vs AWS WAF
| Feature | AWS Shield | AWS WAF |
|---------|-----------|---------|
| **Protection Type** | DDoS attacks | Application-layer attacks |
| **Use For** | Network flooding | SQL injection, XSS, PHP exploits |
| **Rule Management** | Automatic | Configure rules & rule groups |
| **Cost** | Standard (free), Advanced (paid) | Pay per rule + requests |

**🔑 Remember:**
- Shield = DDoS protection (Layer 3/4)
- WAF = Application protection (Layer 7)
- For PHP vulnerabilities → Use **AWS-AWSManagedRulesPHPRuleSet**
- NOT Shield Advanced (that's for DDoS, not app vulnerabilities)

---

## 🏢 4. AWS Organizations & Service Control Policies (SCP)

### ❌ **What You Missed:**
**Question 16**: How SCPs work with parent/child OUs

### ✅ **Key Concepts:**

#### SCP Evaluation Logic
```
User/Role Permission = Intersection of:
├── Identity-based policies (what IAM allows)
└── Service Control Policies (what Org allows)

SCP Inheritance Rules:
Root OU (FullAWSAccess)
└── Project_OU (DENY ec2:DeleteFlowLogs) ← Explicit DENY
    └── Dev_OU (ALLOW ec2:DeleteFlowLogs) ← CANNOT override parent deny
        └── Member Account (IAM allows) ← Still denied by parent SCP
```

#### SCP Key Principles
1. **SCPs are filters, NOT grants**
   - They limit maximum permissions
   - They don't grant permissions themselves

2. **Explicit DENY always wins**
   - Child OUs cannot override parent denies
   - Deny statements are inherited down the tree

3. **Requires BOTH to allow**
   - SCP must allow
   - IAM policy must allow
   - If either denies → Action is denied

**🔑 Remember:**
- Parent OU explicit DENY = Child cannot override
- Default FullAWSAccess = Allows everything (until restricted)
- SCPs act as permission boundaries

---

## 📊 5. CloudWatch Monitoring & SNS Notifications

### ❌ **What You Missed:**
**Question 20**: Monitoring RDS with automated email alerts

### ✅ **Key Concepts:**

#### CloudWatch + SNS Architecture
```
RDS Instance
└── CloudWatch Metrics (CPU, Memory, Disk I/O)
    └── CloudWatch Alarm (threshold breach)
        └── SNS Topic
            ├── Email subscription
            ├── SMS subscription
            └── Lambda subscription
```

#### Common Monitoring Patterns
| Service | Purpose | Example |
|---------|---------|---------|
| **CloudWatch Metrics** | Collect & store metrics | RDS CPU, memory utilization |
| **CloudWatch Alarms** | Trigger on thresholds | CPU > 80% for 5 minutes |
| **SNS** | Send notifications | Email, SMS, HTTP endpoints |
| **SES** | Bulk email sending | Marketing campaigns, NOT alarms |
| **SQS** | Message queuing | Decouple services, NOT notifications |

**🔑 Remember:**
- CloudWatch = Monitoring & Alarms
- SNS = Notification delivery (email, SMS)
- SES = Transactional email (NOT for alarms)
- SQS = Message queue (NOT for direct notifications)

**Correct Pattern:**
```
CloudWatch Alarm → SNS Topic → Email/SMS
```

---

## 💻 6. HPC & High-Performance Networking (EFA vs ENA)

### ❌ **What You Missed:**
**Question 31**: Choosing between EFA and ENA for HPC workloads

### ✅ **Key Concepts:**

#### Network Adapter Comparison
| Feature | ENA (Elastic Network Adapter) | EFA (Elastic Fabric Adapter) |
|---------|------------------------------|------------------------------|
| **Use Case** | General enhanced networking | HPC, ML training |
| **Latency** | Low | Ultra-low |
| **Throughput** | Up to 100 Gbps | Up to 400 Gbps |
| **OS-Bypass** | ❌ No | ✅ Yes (libfabric) |
| **MPI Support** | Limited | Full support |
| **Best For** | Web apps, databases | Tightly-coupled HPC |

#### When to Use Each
```
Standard Workload
└── General web/database apps
    └── Use: Default networking or ENA

High Performance Workload
└── HPC cluster with MPI
    └── Use: EFA (Elastic Fabric Adapter)
        ├── Machine learning training
        ├── Computational fluid dynamics
        └── Weather modeling
```

**🔑 Remember:**
- ENA = Enhanced networking for general use
- EFA = Specialized for HPC with OS-bypass capability
- EFA builds on ENA + adds HPC features
- For "extremely low latency" + "HPC" → Choose **EFA**

---

## 📦 7. ECS Auto Scaling with SQS

### ❌ **What You Missed:**
**Question 35**: Correct metric for scaling ECS tasks

### ✅ **Key Concepts:**

#### ECS Auto Scaling Pattern
```
SQS Queue (Message Backlog)
├── Metric: ApproximateNumberOfMessagesVisible
└── CloudWatch Alarm
    └── Application Auto Scaling
        └── ECS Service (Target Tracking)
            └── Scale tasks based on: Messages per Task

Formula: Desired Tasks = (Queue Depth) / (Messages per Task)
```

#### Scaling Metrics Comparison
| Metric | Good For | Not Suitable For |
|--------|----------|------------------|
| **SQS Queue Depth** | ✅ Work queue scaling | - |
| **ECS CPU/Memory** | General scaling | ❌ Queue processing |
| **S3 Object Count** | - | ❌ Already processed |
| **Running Task Count** | - | ❌ This is the OUTPUT |

**🔑 Remember:**
- Queue depth = Actual work waiting to be done
- Target tracking scaling policy uses queue depth
- Scale ECS tasks to maintain ratio (e.g., 100 messages per task)
- CPU/Memory reflects resource usage, not workload

---

## 💾 8. RDS Automated Backups & Point-in-Time Recovery

### ❌ **What You Missed:**
**Question 40**: How RDS automated backups work

### ✅ **Key Concepts:**

#### RDS Backup Architecture
```
RDS Database Instance
├── Daily Automated Snapshot (during backup window)
│   └── Full snapshot of storage volume
├── Transaction Logs (every 5 minutes)
│   └── Uploaded to S3 continuously
└── Point-in-Time Recovery (PITR)
    └── Combines: Daily snapshot + Transaction logs
        └── Restore to ANY second within retention period
```

#### Backup Components
| Component | Frequency | Purpose |
|-----------|-----------|---------|
| **Storage Volume Snapshot** | Once per day | Full backup baseline |
| **Transaction Logs** | Every 5 minutes | Incremental changes |
| **Retention Period** | 1-35 days | How long backups kept |

#### Point-in-Time Recovery Process
1. RDS takes daily snapshot
2. Transaction logs continuously uploaded to S3
3. To restore: RDS applies logs to snapshot up to target time
4. Creates new DB instance at specified timestamp

**🔑 Remember:**
- NOT every 12 hours → Daily snapshot
- NOT hourly logs → Every 5 minutes
- Logs uploaded CONTINUOUSLY, not just during backup window
- Transaction log backups are AUTOMATIC (don't need manual enable)

---

## 🏗️ 9. AWS Organizations Account Management

### ❌ **What You Missed:**
**Question 52**: Moving management account between organizations

### ✅ **Key Concepts:**

#### Organization Account Migration Process
```
Source Organization (Company A)
├── Management Account
└── Member Accounts (must remove all)
    ↓
Standalone Account (temporary state)
    ↓
Target Organization (Company B)
└── New Member Account (former mgmt account)
```

#### Step-by-Step Process
```
Step 1: Remove all member accounts from source org
├── Sign in as root user
└── Remove each member account

Step 2: Delete source organization
├── Only management account remains
└── Becomes standalone account

Step 3: Accept invitation to target org
├── Target org sends invitation
└── Accept with root credentials
```

**Common Mistakes:**
| ❌ Wrong | ✅ Right |
|---------|---------|
| Can move directly | Must remove members first |
| Contact AWS Support | Self-service process |
| Can leave with members | Must be alone to leave |
| Enable "all features" first | Not required for invitation |

**🔑 Remember:**
- Management account CANNOT leave if members exist
- Must DELETE organization to become standalone
- Then ACCEPT invitation from target organization
- This is a SELF-SERVICE process (no support ticket needed)

---

## 🔐 10. Amazon Cognito & Federated Identity

### ❌ **What You Missed:**
**Question 53**: Cognito + federated identity providers

### ✅ **Key Concepts:**

#### Cognito Architecture for Federation
```
User → Social IdP (Google/Facebook)
    ↓ (Authentication token)
Cognito Identity Pool
    ↓ (Assumes IAM role)
AWS STS (Temporary Credentials)
    ↓
User's mobile app → AWS Services (S3, DynamoDB)
```

#### Two Cognito Services
| Service | Purpose | Provides |
|---------|---------|----------|
| **User Pools** | User directory & authentication | JWT tokens for app |
| **Identity Pools** | AWS credential vending | Temporary AWS credentials |

#### Authentication Flow
```
1. User signs in with Google/Facebook
   ↓
2. External IdP returns authentication token
   ↓
3. App sends token to Cognito Identity Pool
   ↓
4. Identity Pool validates token with IdP
   ↓
5. Identity Pool assumes IAM role
   ↓
6. Returns temporary AWS credentials (STS)
   ↓
7. App uses credentials to call AWS APIs
```

**🔑 Remember:**
- External IdP = Authenticates user
- Cognito Identity Pool = Exchanges token for AWS credentials
- User NEVER receives IAM access keys
- Credentials are TEMPORARY (via STS)
- ONE sign-in (not separate for Cognito + Google)

---

## 🌐 11. CloudFront for S3 Website Performance

### ❌ **What You Missed:**
**Question 62**: Improving S3 website performance globally

### ✅ **Key Concepts:**

#### CloudFront Distribution Architecture
```
S3 Bucket (Origin - us-east-1)
    ↓
CloudFront Distribution
    ├── Edge Location (Tokyo)
    ├── Edge Location (London)
    ├── Edge Location (Sydney)
    └── Edge Location (São Paulo)
        ↓
Users access content from nearest edge location
```

#### Performance Solutions Comparison
| Solution | Use Case | Performance Gain |
|----------|----------|------------------|
| **CloudFront** | ✅ Static content caching | High (edge caching) |
| **S3 Transfer Acceleration** | Uploads to S3 | For PUT/POST only |
| **S3 Cross-Region Replication** | Disaster recovery | Expensive, no edge caching |
| **Application Load Balancer** | ❌ Cannot front S3 | N/A (wrong service) |

#### CloudFront Benefits for S3
1. **Caching** at 400+ edge locations worldwide
2. **Reduced latency** - users hit nearest edge
3. **Reduced S3 costs** - fewer requests to origin
4. **DDoS protection** - AWS Shield Standard included
5. **HTTPS** support with custom SSL certificates

**🔑 Remember:**
- CloudFront = Content delivery network (CDN)
- Caches content at edge locations globally
- Transfer Acceleration = Upload optimization (not download)
- ALB cannot be used as proxy for S3 buckets
- No application changes needed (just DNS update)

---

## 🔗 12. AWS Site-to-Site VPN vs Direct Connect

### ❌ **What You Missed:**
**Question 64**: Fast & cost-effective on-premises to AWS connectivity

### ✅ **Key Concepts:**

#### Connectivity Options Comparison
| Option | Setup Time | Cost | Bandwidth | Encryption | Use When |
|--------|-----------|------|-----------|------------|----------|
| **Site-to-Site VPN** | Minutes | Low | Up to 1.25 Gbps | Yes (IPsec) | ✅ Quick, encrypted |
| **Direct Connect** | Weeks/Months | High | 1-100 Gbps | No (use VPN) | Consistent high throughput |
| **Direct Connect + VPN** | Weeks/Months | Highest | High + encrypted | Yes | Best of both |

#### Site-to-Site VPN Architecture
```
On-Premises Network
├── Customer Gateway (physical device)
│   └── IPsec VPN tunnel over Internet
└── Virtual Private Gateway (AWS side)
    └── VPC
        └── Private Subnets
            └── EC2 Instances
```

#### Quick Decision Tree
```
Need connectivity FAST?
├── YES → Site-to-Site VPN
│   └── Setup in minutes
│   └── Low cost
│   └── Built-in encryption
└── NO → Consider Direct Connect
    └── Takes weeks to provision
    └── Higher cost
    └── Dedicated bandwidth
```

**Common Mistakes:**
| ❌ Wrong Approach | ✅ Right Approach |
|------------------|------------------|
| Direct Connect first | VPN for immediate needs |
| Third-party VPN appliance | AWS Site-to-Site VPN (managed) |
| VPN over Direct Connect | VPN alone is faster to deploy |

**🔑 Remember:**
- Site-to-Site VPN = Quick, cost-effective, encrypted
- Direct Connect = High bandwidth, NOT fast to provision
- For "approaching deadline" → Choose **Site-to-Site VPN**
- AWS manages VPN endpoints (no appliance needed)

---

## 📝 Practice Questions - Focus Areas

### Question 1: S3 Glacier Operations
**Scenario:** A company needs to move files from S3 Glacier Deep Archive to S3 Standard for a new project. What is the correct process?

<details>
<summary>Show Answer</summary>

**Correct Answer:** 
1. Initiate a restore request for the Glacier Deep Archive objects
2. Wait for the restore to complete (12-48 hours)
3. During the restore window, copy the objects to S3 Standard storage class
4. Delete the original Glacier Deep Archive objects

**Why:** Glacier Deep Archive objects cannot be directly copied or moved. You must restore them first, creating a temporary copy in S3 Standard.
</details>

---

### Question 2: Database Migration
**Scenario:** Migrating SQL Server to MySQL requires both schema conversion and data migration with minimal downtime. Which combination of services is correct?

<details>
<summary>Show Answer</summary>

**Correct Answer:** AWS Database Migration Service (DMS) + Schema Conversion Tool (SCT) + AWS Migration Hub

**Why:** 
- SCT converts schema between different engines
- DMS migrates the actual data
- Migration Hub provides centralized tracking
- Together they enable heterogeneous migrations
</details>

---

### Question 3: AWS WAF PHP Protection
**Scenario:** A PHP application needs protection from PHP-specific vulnerabilities like unsafe function injection. What should you configure?

<details>
<summary>Show Answer</summary>

**Correct Answer:** Configure AWS WAF with AWS-AWSManagedRulesPHPRuleSet

**Why:** This managed rule group specifically blocks PHP vulnerability exploits. AWS Shield protects against DDoS, not application-layer attacks.
</details>

---

### Question 4: SCP Inheritance
**Scenario:** Root OU has FullAWSAccess. Project_OU denies ec2:DeleteFlowLogs. Dev_OU (child of Project_OU) allows ec2:DeleteFlowLogs. IAM users in Dev_OU have permissions to delete flow logs. Can they delete?

<details>
<summary>Show Answer</summary>

**Correct Answer:** NO - The explicit deny in Project_OU cannot be overridden by allows in child OUs.

**Why:** SCPs use deny evaluation logic where explicit denies from parent OUs are inherited and cannot be overridden by child OUs.
</details>

---

### Question 5: ECS Auto Scaling
**Scenario:** An ECS service processes messages from an SQS queue. What metric should drive auto scaling?

<details>
<summary>Show Answer</summary>

**Correct Answer:** ApproximateNumberOfMessagesVisible (SQS queue depth)

**Why:** Queue depth represents actual work waiting to be processed. CPU/memory reflects resource usage, not workload. Scale based on: Desired Tasks = Queue Depth / Target Messages per Task
</details>

---

## 🎯 Action Plan for Improvement

### Priority 1: High-Impact Topics (Study First)
1. ✅ **AWS Organizations & SCPs** - Understand parent/child inheritance
2. ✅ **S3 Storage Classes & Glacier** - Know restoration process
3. ✅ **Cognito + Federated Identity** - Understand auth flow

### Priority 2: Service Combinations (Practice Integration)
1. ✅ **DMS + SCT + Migration Hub** - Database migration
2. ✅ **CloudWatch + SNS** - Monitoring & alerting
3. ✅ **ECS + SQS + Auto Scaling** - Queue-based scaling

### Priority 3: Networking & Security
1. ✅ **Site-to-Site VPN vs Direct Connect** - Know when to use each
2. ✅ **EFA vs ENA** - HPC networking
3. ✅ **CloudFront + S3** - Performance optimization

---

## 📚 Recommended Resources

### AWS Documentation
- [S3 Glacier Restore Process](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html)
- [AWS DMS Schema Conversion](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_SchemaConversion.html)
- [AWS WAF Managed Rules](https://docs.aws.amazon.com/waf/latest/developerguide/waf-managed-rule-groups.html)
- [AWS Organizations SCPs](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_evaluation.html)
- [Amazon ECS Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-using-sqs-queue.html)
- [RDS Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)

### Hands-On Labs
1. Practice S3 Glacier restoration workflow
2. Set up CloudWatch Alarms with SNS notifications
3. Configure ECS with SQS queue-based auto scaling
4. Create AWS Organization with SCPs
5. Implement Site-to-Site VPN connection

---

## ✅ Next Steps

1. **Review these notes** - Focus on the "What You Missed" sections
2. **Complete hands-on labs** - Practice is key for retention
3. **Retake similar questions** - Test your understanding
4. **Focus on service combinations** - Many questions test integration knowledge
5. **Review AWS documentation** - Deep dive into weak areas

**Goal:** Achieve 90%+ on your next practice test by mastering these topics!

---

*Last Updated: March 2, 2026*
*Based on Practice Test 3 Results (52/65 - 80%)*

