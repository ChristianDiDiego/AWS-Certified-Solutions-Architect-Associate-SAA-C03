# 📚 Quiz Review - Focus on Incorrect Areas (Practice Test 2)

## 📊 Performance Summary
- **Score**: 49/65 (75.38%)
- **Status**: FAIL (Passing: 72% - 80%)
- **Total Questions**: 65
- **Correct**: 49
- **Incorrect**: 16
- **Review Marked**: 21

---

## 🎯 Domain-wise Performance Analysis

### Design Resilient Architectures (15 questions)
- ✅ Correct: 13
- ❌ Incorrect: 2
- 📌 Review Marked: 6
- **Performance**: 86.7% ⚠️

### Design High-Performing Architectures (20 questions)
- ✅ Correct: 15
- ❌ Incorrect: 5
- 📌 Review Marked: 6
- **Performance**: 75% ⚠️

### Design Secure Architectures (16 questions)
- ✅ Correct: 9
- ❌ Incorrect: 7
- 📌 Review Marked: 5
- **Performance**: 56.25% ❗ **CRITICAL**

### Design Cost-Optimized Architectures (14 questions)
- ✅ Correct: 12
- ❌ Incorrect: 2
- 📌 Review Marked: 4
- **Performance**: 85.7% ⚠️

---

## 🔴 Critical Focus Areas (Incorrect Questions)

### 1. **Amazon Polly - Brand Voice Feature** ❌
**Question**: Which Amazon Polly feature should be used to create a unique voice for branding?

**Your Answer**: Use a Newscaster Speaking Style with Amazon Polly  
**Correct Answer**: Build a Brand Voice using Amazon Polly

**Why You Got It Wrong**:
- Confused speaking styles with brand voice customization
- Newscaster style changes intonation but uses standard voices
- Brand Voice is a custom NTTS engagement for unique vocal identity

**Key Learning**:
```
Amazon Polly Features:
├── Brand Voice (CUSTOM)
│   ├── Custom neural TTS voice
│   ├── Unique to your organization
│   └── Requires engagement with Polly team
│
├── Speaking Styles (STANDARD)
│   ├── Newscaster (news-like delivery)
│   ├── Conversational
│   └── Uses existing Polly voices
│
├── Custom Lexicons
│   └── Pronunciation customization only
│
└── SSML Tags
    └── Control speech parameters (pitch, rate, volume)
```

**Remember**: Brand Voice = Custom voice creation | Speaking Styles = Existing voice modulation

---

### 2. **CloudFront + ACM Certificate Region** ❌
**Question**: Where should SSL/TLS certificate be imported for CloudFront?

**Your Answer**: Import into ACM in US West (N. California) Region  
**Correct Answer**: Import into ACM in US East (N. Virginia) Region (us-east-1)

**Why You Got It Wrong**:
- Missed the CloudFront-specific requirement
- Thought application region determines certificate region

**Critical Rule**:
```
🔒 CloudFront Certificate Requirement:
   MUST be in us-east-1 (N. Virginia) ONLY
   
   Even if your application runs in:
   ✗ us-west-1
   ✗ eu-west-1
   ✗ ap-south-1
   
   Certificate MUST be in: us-east-1 ✓
```

**Why us-east-1?**
- CloudFront is a global service
- Control plane is in us-east-1
- All CloudFront certificates are validated against us-east-1 ACM

**Complete Setup**:
1. Request/Import certificate in ACM (us-east-1)
2. Add alternate domain names (CNAMEs) to CloudFront
3. Associate ACM certificate with CloudFront distribution
4. Create Route 53 alias record

---

### 3. **S3 Glacier - Cost Optimization Strategy** ❌
**Question**: How to minimize S3 Glacier retrieval costs for unpredictable access?

**Your Answer**: Use S3 Glacier Instant Retrieval  
**Correct Answer**: Use S3 lifecycle policies to move data to S3 Glacier Flexible Retrieval and keep retrievals within free quota

**Why You Got It Wrong**:
- Focused on instant access instead of cost optimization
- Overlooked the free retrieval quota feature

**S3 Glacier Retrieval Costs Comparison**:
```
Storage Class          | Retrieval Time  | Cost Strategy
─────────────────────────────────────────────────────────
Glacier Instant       | Milliseconds    | Pay per GB retrieved
Retrieval             |                 | Most expensive retrieval
                      |                 | ❌ Not cost-optimized
─────────────────────────────────────────────────────────
Glacier Flexible      | Minutes-Hours   | 10 GB/month FREE quota
Retrieval             |                 | After quota: $0.01-$0.03/GB
                      |                 | ✅ Cost-optimized for occasional use
─────────────────────────────────────────────────────────
Glacier Deep Archive  | 12-48 hours     | Bulk: $0.0025/GB (cheap)
                      |                 | But too slow for most use cases
```

**Key Strategy**:
- Use **Flexible Retrieval** for unpredictable access patterns
- Stay within **10 GB free monthly quota**
- Plan retrievals to avoid exceeding quota

---

### 4. **S3 Cross-Account Access - Bucket Policy** ❌
**Question**: How should audit firm access retail company's S3 bucket logs?

**Your Answer**: Add permissions in IAM policy, SCP, and bucket policy  
**Correct Answer**: Attach bucket policy granting audit firm's AWS account read/write permissions with aws:SecureTransport

**Why You Got It Wrong**:
- Over-complicated the solution with SCPs
- SCPs don't grant cross-account access (they only restrict within an organization)

**Critical Concept**:
```
❌ WRONG Understanding:
   SCP = Service Control Policy = Grants permissions ✗
   
✅ CORRECT Understanding:
   SCP = Organization-level DENY policy
   - Only restricts accounts within YOUR organization
   - Cannot grant access to EXTERNAL accounts
   - Works ONLY within AWS Organizations

Cross-Account Access Methods:
├── Bucket Policy (S3)
│   ├── Can grant access to external accounts
│   ├── Resource-based policy
│   └── ✅ Best for S3 cross-account access
│
├── IAM Role (Cross-Account)
│   ├── External account assumes role
│   ├── Requires trust relationship
│   └── Good for temporary access
│
└── SCP (AWS Organizations)
    ├── Only restricts within YOUR org
    ├── Cannot grant to external accounts
    └── ❌ Not for cross-account grants
```

**Correct Bucket Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "AllowAuditFirmAccess",
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::AUDIT-ACCOUNT-ID:root"
    },
    "Action": [
      "s3:GetObject",
      "s3:ListBucket"
    ],
    "Resource": [
      "arn:aws:s3:::company-logs",
      "arn:aws:s3:::company-logs/*"
    ],
    "Condition": {
      "Bool": {
        "aws:SecureTransport": "true"
      }
    }
  }]
}
```

---

### 5. **VPC Flow Logs - Subnet Level** ❌
**Question**: How to troubleshoot EC2 outbound connection failures in specific subnet?

**Your Answer**: Create VPC Flow Log for entire VPC and filter in CloudWatch  
**Correct Answer**: Create flow log for subnet 10.10.55.0/24

**Why You Got It Wrong**:
- Went for broader scope instead of targeted approach
- Didn't consider log volume and cost implications

**Flow Logs Scoping Strategy**:
```
Scope Level        | Coverage        | Log Volume | Cost    | Use Case
─────────────────────────────────────────────────────────────────────
VPC-Level         | All 100 subnets | Very High  | $$$     | Initial diagnosis
                  |                 |            |         | ❌ Too much data
─────────────────────────────────────────────────────────────────────
Subnet-Level      | 1 subnet only   | Moderate   | $      | Specific troubleshooting
                  |                 |            |         | ✅ Targeted & cost-effective
─────────────────────────────────────────────────────────────────────
ENI-Level         | Single instance | Low        | $      | Instance-specific issues
                  |                 |            |         | ⚠️ 50 configs needed
```

**Best Practice**:
1. Start with **subnet-level** logs for affected subnet
2. If issue spreads, escalate to VPC-level
3. Use ENI-level only for single-instance deep dive

---

### 6. **Amazon Comprehend - Data Ownership** ❌
**Question**: Who retains ownership of content processed by Amazon Comprehend?

**Your Answer**: Both AWS and the customer  
**Correct Answer**: Customer

**Why You Got It Wrong**:
- Misunderstood AWS data processing model
- Confused data processor role with data ownership

**AWS Data Privacy Model**:
```
┌─────────────────────────────────────────────────┐
│ Customer (Data Owner)                           │
│ ├── Owns ALL content uploaded to AWS           │
│ ├── Controls access to data                    │
│ ├── Can delete data anytime                    │
│ └── AWS cannot use without permission          │
└─────────────────────────────────────────────────┘
         │
         │ Uploads Data
         ▼
┌─────────────────────────────────────────────────┐
│ AWS (Data Processor)                            │
│ ├── Processes data ONLY to provide service     │
│ ├── Does NOT own customer data                 │
│ ├── Cannot use data for other purposes         │
│ └── Subject to customer's instructions         │
└─────────────────────────────────────────────────┘
```

**Key Principles**:
- **Customer ALWAYS owns the content**
- AWS is a **data processor**, not owner
- AWS uses content ONLY to provide the service
- Data is never shared without customer consent

---

### 7. **Aurora MySQL - IAM Authentication Plugin** ❌
**Question**: Correct command to configure AWSAuthenticationPlugin?

**Your Answer**: `CREATE USER iam_db_user IDENTIFIED WITH AWSAuthenticationPlugin;`  
**Correct Answer**: `CREATE USER iam_db_user IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';`

**Why You Got It Wrong**:
- Missing the required `AS 'RDS'` clause
- Incomplete syntax for IAM authentication

**Correct Syntax**:
```sql
-- ✅ CORRECT
CREATE USER iam_db_user 
IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';

-- ❌ INCOMPLETE (missing AS 'RDS')
CREATE USER iam_db_user 
IDENTIFIED WITH AWSAuthenticationPlugin;
```

**Complete IAM DB Authentication Setup**:
```sql
-- 1. Create IAM-authenticated user
CREATE USER iam_db_user 
IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';

-- 2. Grant permissions
GRANT SELECT, INSERT, UPDATE ON mydb.* TO iam_db_user;

-- 3. Application connects with IAM token
mysql --host=mydb.region.rds.amazonaws.com \
      --port=3306 \
      --user=iam_db_user \
      --password="$(aws rds generate-db-auth-token \
        --hostname mydb.region.rds.amazonaws.com \
        --port 3306 \
        --username iam_db_user)" \
      --enable-cleartext-plugin
```

**Why `AS 'RDS'` is Required**:
- Specifies authentication method source
- Tells MySQL to validate against RDS IAM
- Without it, authentication will fail

---

### 8. **S3 Client-Side Encryption with KMS** ❌
**Question**: How to encrypt data before uploading to S3 with AWS-managed keys?

**Your Answer**: Enable server-side encryption with S3-managed keys (SSE-S3)  
**Correct Answer**: Use client-side encryption with AWS KMS and customer-managed CMK

**Why You Got It Wrong**:
- Confused server-side with client-side encryption
- Didn't read "encrypt before uploading" requirement

**Encryption Methods Comparison**:
```
┌─────────────────────────────────────────────────────────────┐
│ SERVER-SIDE ENCRYPTION (SSE)                                │
│ Encryption happens AFTER data reaches S3                    │
├─────────────────────────────────────────────────────────────┤
│ SSE-S3   │ AWS-managed keys | Free | Automatic             │
│ SSE-KMS  │ AWS KMS keys     | Paid | Audit trail           │
│ SSE-C    │ Customer keys    | Free | Customer manages key  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ CLIENT-SIDE ENCRYPTION                                       │
│ Encryption happens BEFORE data leaves application           │
├─────────────────────────────────────────────────────────────┤
│ Client +  │ Customer provides key | Full control            │
│ Own Key   │ Data encrypted locally | Complex management    │
│           │                                                  │
│ Client +  │ KMS provides data key | AWS manages CMK        │
│ KMS CMK   │ Data encrypted locally | ✅ Best practice      │
│           │ Audit trail in CloudTrail |                     │
└─────────────────────────────────────────────────────────────┘
```

**Client-Side Encryption with KMS Flow**:
```
1. Application requests data key from KMS
   ↓
2. KMS returns encrypted + plaintext data key
   ↓
3. Application encrypts data with plaintext key
   ↓
4. Application uploads encrypted data + encrypted key to S3
   ↓
5. Application deletes plaintext key from memory

On Retrieval:
1. Download encrypted data + encrypted key from S3
   ↓
2. Send encrypted key to KMS for decryption
   ↓
3. KMS returns plaintext key
   ↓
4. Application decrypts data with plaintext key
```

---

### 9. **Transit Gateway + NACL Rules** ❌
**Question**: How do NACL outbound rules evaluate traffic to Transit Gateway?

**Your Answer**: Outbound rules use source IP address  
**Correct Answer**: Outbound rules use destination IP address

**Why You Got It Wrong**:
- Confused source and destination in outbound rules
- Didn't understand NACL evaluation logic

**NACL Rule Evaluation**:
```
┌─────────────────────────────────────────────────────────┐
│ INBOUND Rules                                           │
├─────────────────────────────────────────────────────────┤
│ Evaluate: SOURCE IP address                            │
│ Question: "Where is traffic coming FROM?"               │
│                                                         │
│ Example:                                                │
│ Allow traffic FROM 10.0.1.0/24                         │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│ OUTBOUND Rules                                          │
├─────────────────────────────────────────────────────────┤
│ Evaluate: DESTINATION IP address ✅                     │
│ Question: "Where is traffic going TO?"                  │
│                                                         │
│ Example:                                                │
│ Allow traffic TO 10.0.2.0/24 (Transit Gateway subnet)  │
└─────────────────────────────────────────────────────────┘
```

**Memory Trick**:
```
INBOUND  = FROM (source)
OUTBOUND = TO (destination)

"OUT" = "TO where you're going OUT"
```

**Transit Gateway NACL Configuration**:
```
# EC2 Subnet NACL (10.0.1.0/24)
Outbound Rule:
- Rule: 100
- Type: All Traffic
- Destination: 10.0.2.0/24 (TGW subnet) ✅
- Action: ALLOW

# Transit Gateway Subnet NACL (10.0.2.0/24)
Inbound Rule:
- Rule: 100
- Type: All Traffic
- Source: 10.0.1.0/24 (EC2 subnet) ✅
- Action: ALLOW
```

---

### 10. **Aurora Disk Failure Protection** ❌
**Question**: How to protect Aurora from disk failure without data loss?

**Your Answer**: Add Aurora Replicas in different AZs with crash recovery  
**Correct Answer**: Add Aurora Replicas in different AZs with storage auto-repair

**Why You Got It Wrong**:
- Confused crash recovery with storage auto-repair
- Both are Aurora features but serve different purposes

**Aurora Reliability Features**:
```
┌──────────────────────────────────────────────────────────┐
│ Storage Auto-Repair (Data Protection)                    │
├──────────────────────────────────────────────────────────┤
│ ✅ Protects against: DISK FAILURE                        │
│ • Maintains 6 copies across 3 AZs                        │
│ • Automatically repairs corrupted segments               │
│ • No data loss from disk failure                         │
│ • No manual intervention needed                          │
│                                                          │
│ Use Case: Disk hardware failure                         │
└──────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│ Crash Recovery (Fast Restart)                            │
├──────────────────────────────────────────────────────────┤
│ ✅ Protects against: DATABASE CRASH                      │
│ • Parallel, asynchronous recovery                        │
│ • Database available faster after crash                  │
│ • Survivable cache reduces recovery time                 │
│                                                          │
│ Use Case: Database process crash or restart             │
└──────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│ Survivable Page Cache (Performance)                      │
├──────────────────────────────────────────────────────────┤
│ • Separate process for page cache                        │
│ • Cache survives DB restart                              │
│ • Faster reads after recovery                            │
└──────────────────────────────────────────────────────────┘
```

**Complete Aurora HA Setup**:
```
Primary DB Instance (us-east-1a)
├── Storage: 6 copies across 3 AZs
│   └── Auto-repair enabled ✅
│
├── Aurora Replica 1 (us-east-1b)
│   └── Automatic failover target
│
└── Aurora Replica 2 (us-east-1c)
    └── Additional redundancy

Features:
✅ Storage auto-repair = Disk failure protection
✅ Multi-AZ replicas = Instance failure protection
✅ Crash recovery = Fast restart
✅ Survivable cache = Performance optimization
```

---

### 11. **CloudWatch Execution Logging for API Gateway** ❌
**Question**: How to log API Gateway request/response payloads?

**Your Answer**: Use CloudTrail logging  
**Correct Answer**: Use CloudWatch execution logging

**Why You Got It Wrong**:
- Confused CloudTrail (audit) with CloudWatch (application logs)
- Didn't understand API Gateway logging types

**API Gateway Logging Types**:
```
┌────────────────────────────────────────────────────────┐
│ CloudWatch ACCESS Logging                              │
├────────────────────────────────────────────────────────┤
│ Logs: WHO called, WHEN, from WHERE                    │
│ • Caller IP                                            │
│ • Request time                                         │
│ • HTTP method and path                                 │
│ • Status code                                          │
│                                                        │
│ Does NOT include: Request/response BODY ❌             │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│ CloudWatch EXECUTION Logging ✅                        │
├────────────────────────────────────────────────────────┤
│ Logs: COMPLETE request/response cycle                 │
│ • Request payload ✅                                   │
│ • Response payload ✅                                  │
│ • Error messages                                       │
│ • Integration latency                                  │
│ • Authorization results                                │
│                                                        │
│ Use for: Debugging, troubleshooting                   │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│ CloudTrail (API Activity)                              │
├────────────────────────────────────────────────────────┤
│ Logs: API Gateway MANAGEMENT operations               │
│ • CreateRestApi                                        │
│ • UpdateStage                                          │
│ • DeleteDeployment                                     │
│                                                        │
│ Does NOT log: Application requests ❌                  │
│ Use for: Audit, compliance                            │
└────────────────────────────────────────────────────────┘
```

**When to Use Each**:
```
Access Logging → WHO accessed WHAT
Execution Logging → WHAT data was exchanged (debugging)
CloudTrail → WHO changed API configuration
```

---

### 12. **S3 Encryption - Customer Managed Keys** ❌
**Question**: Most secure encryption for S3 with customer-managed keys?

**Your Answer**: SSE-S3  
**Correct Answer**: SSE-KMS with customer-managed CMK

**Why You Got It Wrong**:
- Chose AWS-managed over customer-managed
- Didn't read "customer-managed key" requirement

**S3 Encryption Decision Tree**:
```
Need customer-managed keys?
│
├─ NO → SSE-S3
│       ├── AWS manages keys
│       ├── Free
│       └── No audit trail
│
└─ YES → SSE-KMS with Customer CMK ✅
         ├── Customer manages key policy
         ├── Full audit trail in CloudTrail
         ├── Key rotation control
         └── Fine-grained access control

Alternative (Complex):
└─ Client-Side Encryption
   ├── Encrypt before upload
   ├── Customer manages keys entirely
   └── More operational overhead
```

**SSE-KMS with CMK Features**:
```json
{
  "Compliance Requirements": [
    "✅ Customer-managed key",
    "✅ Audit logging (CloudTrail)",
    "✅ Key rotation control",
    "✅ Fine-grained IAM policies",
    "✅ Cross-account access support"
  ],
  
  "Key Policy Example": {
    "Version": "2012-10-17",
    "Statement": [{
      "Sid": "Enable IAM policies",
      "Effect": "Allow",
      "Principal": {"AWS": "arn:aws:iam::ACCOUNT:root"},
      "Action": "kms:*",
      "Resource": "*"
    }, {
      "Sid": "Allow S3 to use key",
      "Effect": "Allow",
      "Principal": {"Service": "s3.amazonaws.com"},
      "Action": [
        "kms:Decrypt",
        "kms:GenerateDataKey"
      ],
      "Resource": "*"
    }]
  }
}
```

---

### 13. **Global Accelerator vs Multi-Region ALB** ❌
**Question**: How to provide global low latency for EC2-backed web app?

**Your Answer**: Deploy multiple ALBs in different Regions  
**Correct Answer**: Use AWS Global Accelerator in front of the ALB

**Why You Got It Wrong**:
- Didn't consider the complexity of multi-region management
- Missed the need for anycast IP and automatic routing

**Solution Comparison**:
```
┌──────────────────────────────────────────────────────┐
│ Multiple Regional ALBs (Your Answer) ❌              │
├──────────────────────────────────────────────────────┤
│ Challenges:                                          │
│ • Need Route 53 for DNS routing                     │
│ • DNS caching delays (TTL)                          │
│ • Multiple IPs to manage                            │
│ • Manual health check configuration                 │
│ • No automatic performance-based routing            │
│                                                      │
│ Complexity: HIGH                                     │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│ AWS Global Accelerator (Correct Answer) ✅          │
├──────────────────────────────────────────────────────┤
│ Benefits:                                            │
│ • 2 static anycast IPs                              │
│ • Automatic routing to optimal endpoint             │
│ • Health checks built-in                            │
│ • Instant regional failover                         │
│ • Traffic dials for deployment testing              │
│ • Uses AWS global network (not internet)            │
│                                                      │
│ Complexity: LOW                                      │
└──────────────────────────────────────────────────────┘
```

**Global Accelerator Architecture**:
```
┌─────────────────────────────────────────────┐
│ User Request                                │
│ ↓                                           │
│ Global Accelerator Edge Location (Nearest) │
│ ↓                                           │
│ AWS Global Network (Private)                │
│ ↓                                           │
│ ┌─────────────┬──────────────┬────────────┐│
│ │ ALB         │ ALB          │ ALB        ││
│ │ us-east-1   │ eu-west-1    │ ap-south-1 ││
│ └─────────────┴──────────────┴────────────┘│
│           Automatic Health Checks            │
└─────────────────────────────────────────────┘

Features:
✅ 2 static IPs (anycast)
✅ Automatic optimal endpoint selection
✅ Instant failover (no DNS delay)
✅ Performance-based routing
✅ TCP/UDP termination at edge
```

---

### 14. **API Gateway Private Integration** ❌
**Question**: How to restrict API Gateway backend to accept only from API Gateway?

**Your Answer**: Use security group to allow only API Gateway's public IPs  
**Correct Answer**: Use VPC endpoint and private integration with API Gateway

**Why You Got It Wrong**:
- API Gateway IPs are dynamic and unpredictable
- Security groups with public IPs won't work reliably

**Why Public IP Approach Fails**:
```
❌ Problem with Public IP Whitelisting:
   1. API Gateway IPs constantly change
   2. IP ranges are not publicly documented
   3. Managed service = AWS controls IPs
   4. Security group rules would break frequently
   
✅ Solution: Private Integration
   1. Backend stays in private subnet
   2. VPC endpoint enables private access
   3. No public IPs involved
   4. Security group allows VPC endpoint only
```

**Private API Gateway Architecture**:
```
┌──────────────────────────────────────────────────┐
│ PUBLIC                                           │
│ ┌────────────────────────────────┐              │
│ │ API Gateway (Regional/Edge)    │              │
│ │ ├─ Execute-api endpoint        │              │
│ │ └─ Resource policy             │              │
│ └────────────────────────────────┘              │
└──────────────────────────────────────────────────┘
                │
                │ Private Integration
                ▼
┌──────────────────────────────────────────────────┐
│ VPC                                              │
│ ┌────────────────────────────────┐              │
│ │ VPC Endpoint (Interface)       │              │
│ │ ├─ ENI with private IP         │              │
│ │ └─ Security Group              │              │
│ └────────────────────────────────┘              │
│                │                                 │
│                ▼                                 │
│ ┌────────────────────────────────┐              │
│ │ Private Subnet                 │              │
│ │ ├─ EC2 Backend                 │              │
│ │ └─ ALB (Internal)              │              │
│ └────────────────────────────────┘              │
└──────────────────────────────────────────────────┘
```

**Security Group Configuration**:
```
EC2/ALB Security Group:
Inbound Rules:
├─ Type: HTTPS
├─ Port: 443
├─ Source: sg-vpc-endpoint (VPC Endpoint SG)
└─ Description: Allow from API Gateway VPC endpoint only

VPC Endpoint Security Group:
Inbound Rules:
├─ Type: HTTPS
├─ Port: 443
├─ Source: 0.0.0.0/0 (or specific CIDR)
└─ Description: Accept API requests
```

---

### 15. **Lambda + SQS IAM Permissions** ❌
**Question**: Most secure way to grant Lambda access to SQS?

**Your Answer**: Create resource-based policy on Lambda function  
**Correct Answer**: Attach IAM role to Lambda with SQS read permissions

**Why You Got It Wrong**:
- Confused Lambda resource policy direction
- Lambda resource policy is for ALLOWING INVOCATION, not for ACCESSING other services

**Lambda Permission Model**:
```
┌────────────────────────────────────────────────────┐
│ Lambda EXECUTION Role (IAM Role)                   │
│ ┌────────────────────────────────────────────────┐ │
│ │ Purpose: What Lambda CAN DO                    │ │
│ │ Direction: Lambda → Other AWS Services         │ │
│ │                                                 │ │
│ │ Grants Lambda permission to:                   │ │
│ │ • Read from SQS ✅                             │ │
│ │ • Write to DynamoDB                            │ │
│ │ • Put logs in CloudWatch                       │ │
│ │ • Decrypt with KMS                             │ │
│ └────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────┐
│ Lambda RESOURCE Policy                             │
│ ┌────────────────────────────────────────────────┐ │
│ │ Purpose: WHO can invoke Lambda                 │ │
│ │ Direction: Other Services → Lambda             │ │
│ │                                                 │ │
│ │ Allows invocation by:                          │ │
│ │ • API Gateway                                  │ │
│ │ • S3 events                                    │ │
│ │ • SNS                                          │ │
│ │ • Other AWS accounts                           │ │
│ └────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────┘
```

**Correct IAM Role Policy**:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "sqs:ReceiveMessage",
      "sqs:DeleteMessage",
      "sqs:GetQueueAttributes"
    ],
    "Resource": "arn:aws:sqs:region:account:queue-name"
  }, {
    "Effect": "Allow",
    "Action": [
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:PutLogEvents"
    ],
    "Resource": "*"
  }]
}
```

**Lambda → SQS Integration**:
```
1. Create IAM Execution Role
   └─ Attach SQS read permissions

2. Attach role to Lambda function

3. Configure Lambda event source mapping
   └─ Lambda polls SQS automatically

4. Lambda processes messages
   └─ Deletes from queue on success
```

---

### 16. **EC2 Boot Volume Mounting Issue** ❌
**Question**: Why did EC2 boot from wrong volume after adding new EBS?

**Your Answer**: Instance uses wrong boot AMI  
**Correct Answer**: Data volume had duplicate filesystem label

**Why You Got It Wrong**:
- Focused on AMI instead of filesystem-level configuration
- Didn't understand filesystem label conflicts

**Root Cause Explanation**:
```
┌─────────────────────────────────────────────────┐
│ Problem: Duplicate Filesystem Labels            │
├─────────────────────────────────────────────────┤
│                                                 │
│ Root Volume (Original):                         │
│ ├─ Device: /dev/xvda                           │
│ ├─ Filesystem Label: "/" or "root"            │
│ └─ Contains: OS files                          │
│                                                 │
│ Data Volume (New):                              │
│ ├─ Device: /dev/xvdf                           │
│ ├─ Filesystem Label: "/" or "root" ⚠️         │
│ └─ Contains: Data files                        │
│                                                 │
│ On Boot:                                        │
│ └─ OS searches for label "root"                │
│     ├─ Finds TWO volumes with same label       │
│     └─ Mounts WRONG volume (data) as root ❌   │
└─────────────────────────────────────────────────┘
```

**Filesystem Label Management**:
```bash
# View filesystem labels
sudo e2label /dev/xvda1  # Shows: "/"
sudo e2label /dev/xvdf1  # Shows: "/" (DUPLICATE!)

# Fix: Change data volume label
sudo e2label /dev/xvdf1 data-volume

# Verify
sudo e2label /dev/xvdf1  # Shows: "data-volume" ✅

# Update /etc/fstab to use UUID instead
sudo blkid  # Get UUIDs
sudo nano /etc/fstab

# Instead of:
LABEL=/  /  ext4  defaults  0 0

# Use UUID:
UUID=abc123  /  ext4  defaults  0 0
```

**Prevention Best Practices**:
```
1. ✅ Use UUIDs in /etc/fstab (not labels)
   └─ UUIDs are guaranteed unique

2. ✅ Check labels before attaching volumes
   └─ sudo e2label /dev/device

3. ✅ Use descriptive unique labels
   ├─ Root volume: "root" or "os"
   ├─ Data volume: "data-mysql" or "app-logs"
   └─ Never reuse label names

4. ✅ Test boot after adding volumes
   └─ Reboot in non-production first
```

---

## 📋 Quick Reference - Common Mistakes

### 1. **Service Control Policies (SCPs)**
```
✅ SCPs = DENY policies within your AWS Organization
❌ SCPs ≠ Grant permissions to external accounts
   
For cross-account access:
└─ Use Bucket Policies or IAM Roles
```

### 2. **CloudFront Certificate Region**
```
✅ Always us-east-1 for CloudFront
❌ Not the application's region
   
Memory: CloudFront = Global = us-east-1
```

### 3. **NACL Rule Evaluation**
```
Inbound:  Source IP (WHERE FROM?)
Outbound: Destination IP (WHERE TO?)
   
"OUT" = "TO where you're going"
```

### 4. **API Gateway Logging**
```
Access Logging:    WHO called (metadata)
Execution Logging: WHAT was sent (payload) ✅
CloudTrail:        Management operations
```

### 5. **Lambda Permissions**
```
Execution Role:  What Lambda CAN DO (→ other services)
Resource Policy: WHO can invoke Lambda (← other services)
   
For SQS access: Use Execution Role ✅
```

### 6. **S3 Encryption**
```
Customer-managed key required?
├─ NO  → SSE-S3
└─ YES → SSE-KMS with CMK ✅
   
Client-side: Encrypt BEFORE upload
Server-side: Encrypt AFTER upload
```

### 7. **Aurora Reliability**
```
Disk Failure:     Storage Auto-Repair ✅
Database Crash:   Crash Recovery
Fast Recovery:    Survivable Cache
```

### 8. **Data Ownership in AWS**
```
Customer ALWAYS owns content
AWS = Data Processor (not owner)
   
Even for:
├─ Comprehend
├─ Rekognition
├─ Polly
└─ All AWS services
```

---

## 🎯 Action Items

### Critical Areas (Review Now!)
1. **Design Secure Architectures**: 56.25% - NEEDS IMMEDIATE ATTENTION
   - Cross-account access (Bucket Policies vs SCPs)
   - S3 encryption methods (SSE-S3, SSE-KMS, Client-side)
   - IAM roles vs resource policies
   - Data ownership in AWS

2. **Design High-Performing Architectures**: 75%
   - Amazon Polly features (Brand Voice vs Speaking Styles)
   - Global Accelerator vs Multi-region deployments
   - NACL evaluation (source vs destination)
   - Aurora reliability features

### Study Recommendations

**This Week**:
- [ ] Review all 16 incorrect questions in detail
- [ ] Practice IAM policies and cross-account access scenarios
- [ ] Study S3 encryption decision tree
- [ ] Memorize CloudFront certificate region requirement (us-east-1)

**Next Week**:
- [ ] Take practice test focusing on Security domain
- [ ] Lab: Configure cross-account S3 access
- [ ] Lab: Set up API Gateway with private integration
- [ ] Lab: IAM authentication for Aurora MySQL

**Resources to Review**:
1. AWS Security Best Practices whitepaper
2. S3 encryption documentation
3. IAM policy evaluation logic
4. Aurora reliability and availability

---

## 💡 Exam Tips

### Pattern Recognition
```
Question asks about...          → Consider...
─────────────────────────────────────────────────
"Customer-managed key"          → SSE-KMS with CMK
"Cross-account S3 access"       → Bucket Policy
"CloudFront certificate"        → us-east-1 ACM
"Client-side encryption"        → Encrypt before upload
"Lambda access to services"     → Execution Role
"Who owns data in AWS?"         → Customer always
```

### Elimination Strategy
```
If you see:
├─ "SCP grants external access"      → Eliminate ❌
├─ "CloudTrail logs application API" → Eliminate ❌
├─ "Lambda resource policy for SQS"  → Eliminate ❌
├─ "Newscaster = Custom voice"       → Eliminate ❌
└─ "AWS owns customer data"          → Eliminate ❌
```

---

## 📊 Next Practice Test Goals

**Target Areas**:
- Design Secure Architectures: Improve from 56% to 85%+
- Design High-Performing: Improve from 75% to 90%+

**Pass Requirements**:
- Score: 72%+ (ideally 80%+)
- Security: 85%+ (critical domain)
- No domain below 75%

**Study Focus** (in order):
1. Security (CRITICAL)
2. High-Performance
3. Resilient Architectures
4. Cost Optimization

---

## 📚 Additional Resources

### Documentation to Review
1. [S3 Encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingEncryption.html)
2. [Cross-Account Access](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_aws-accounts.html)
3. [Aurora Reliability](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html)
4. [API Gateway Logging](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html)

### Practice Labs
1. Configure S3 bucket policy for cross-account access
2. Set up Aurora with IAM authentication
3. Create API Gateway with private integration
4. Configure Lambda execution role for multiple services

---

**Last Updated**: March 2, 2026  
**Quiz ID**: 60504  
**Attempt**: 1 of ∞  
**Next Review**: Before next practice test

