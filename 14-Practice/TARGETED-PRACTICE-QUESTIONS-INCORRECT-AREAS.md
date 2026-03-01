# 🎯 Targeted Practice Questions - Focus on Weak Areas

## Based on Practice Test 2 Results (49/65 - 75.38%)

---

## Category 1: Amazon Polly & ML Services ❌

### Question 1
A media company wants to create audiobooks with multiple unique character voices that sound natural and consistent throughout the book. They need each character to have a distinct vocal identity. Which Amazon Polly feature should they use?

A. Use multiple standard NTTS voices with SSML tags  
B. Create custom lexicons for each character  
C. Build Brand Voices for each character  
D. Use Newscaster speaking style with different parameters

<details>
<summary>Show Answer</summary>

**Answer: C**

**Explanation:**
Brand Voice is Amazon Polly's feature for creating custom, unique vocal identities. It's designed for organizations wanting proprietary voices that are:
- Completely unique
- Consistent across all content
- Custom neural TTS (NTTS) voices

**Why others are wrong:**
- **A**: SSML tags modify existing voices, don't create new ones
- **B**: Lexicons only change pronunciation, not voice characteristics
- **D**: Newscaster is a speaking style, uses standard voices

**Key Learning:**
```
Polly Features Hierarchy:
├── Brand Voice (Custom Voice Creation)
│   └── Completely new vocal identity
├── Speaking Styles (Voice Modulation)
│   └── Newscaster, Conversational
├── Lexicons (Pronunciation)
│   └── Custom word pronunciation
└── SSML (Fine Control)
    └── Pitch, rate, volume
```

**Reference:** https://aws.amazon.com/polly/features/
</details>

---

### Question 2
A company is implementing Amazon Comprehend to analyze customer feedback. The security team asks who owns the customer data processed by Comprehend. What should you tell them?

A. AWS owns the data and can use it for service improvements  
B. The customer retains full ownership of all data  
C. Ownership is shared between AWS and the customer  
D. Data becomes AWS property after processing

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
AWS has a clear data ownership policy:
- **Customer ALWAYS owns their content**
- AWS acts as a **data processor**, not owner
- AWS processes data ONLY to provide the service
- Data is NEVER used without customer permission

**AWS Data Privacy Principles:**
```
Customer:
├── Owns ALL uploaded content
├── Controls data access and use
├── Can delete data anytime
└── AWS needs permission to process

AWS:
├── Processes data ONLY for service delivery
├── Does NOT own customer data
├── Cannot use data for other purposes
└── Subject to customer's instructions
```

**This applies to ALL AWS services:**
- Comprehend
- Rekognition
- Polly
- Textract
- All others

**Reference:** https://aws.amazon.com/compliance/data-privacy-faq/
</details>

---

## Category 2: CloudFront & Certificate Management ❌❌

### Question 3
You need to use a custom domain (www.example.com) with CloudFront for a global application. The application backend runs in eu-west-1. Where should you request/import the ACM certificate?

A. In eu-west-1 where the application runs  
B. In us-east-1 (N. Virginia)  
C. In all regions where content will be cached  
D. In the region closest to most users

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**CloudFront Certificate Rule:**
- Certificates for CloudFront MUST be in **us-east-1**
- This applies regardless of:
  - Where your application runs
  - Where your users are located
  - Your preferred region

**Why us-east-1?**
- CloudFront is a global service
- Control plane operates from us-east-1
- All distributions validate certificates against us-east-1

**Complete Setup:**
```
1. Request/Import certificate
   └─ Region: us-east-1 (MANDATORY)
   
2. Add alternate domain names to CloudFront
   └─ Example: www.example.com, example.com
   
3. Associate ACM certificate with distribution
   └─ Select certificate from us-east-1
   
4. Update DNS (Route 53)
   └─ Create alias record to CloudFront
```

**Memory Trick:**
```
CloudFront = Global Service
Global HQ = us-east-1
Therefore: Certificate = us-east-1
```

**Reference:** https://docs.aws.amazon.com/acm/latest/userguide/acm-regions.html
</details>

---

### Question 4
A company uses CloudFront to distribute content from an S3 bucket. The S3 bucket is encrypted with a customer-managed KMS key. Users report intermittent access denied errors. What is the MOST likely cause?

A. CloudFront distribution is in the wrong region  
B. KMS key policy doesn't allow CloudFront service principal  
C. S3 bucket policy is missing  
D. Certificate is in wrong region

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
When CloudFront accesses encrypted S3 objects, it needs permission to use the KMS key.

**Required KMS Key Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "Allow CloudFront",
    "Effect": "Allow",
    "Principal": {
      "Service": "cloudfront.amazonaws.com"
    },
    "Action": [
      "kms:Decrypt",
      "kms:GenerateDataKey"
    ],
    "Resource": "*",
    "Condition": {
      "StringEquals": {
        "aws:SourceArn": "arn:aws:cloudfront::ACCOUNT:distribution/DISTRIBUTION-ID"
      }
    }
  }]
}
```

**Complete Permission Chain:**
```
User Request
  ↓
CloudFront (needs OAI permissions)
  ↓
S3 Bucket (needs bucket policy)
  ↓
KMS Key (needs key policy) ✅
  ↓
Encrypted Object
```

**Why others are wrong:**
- **A**: CloudFront is global, no region
- **C**: Would cause consistent failures, not intermittent
- **D**: Certificate issues affect HTTPS, not S3 access

**Reference:** https://docs.aws.amazon.com/kms/latest/developerguide/services-cloudfront.html
</details>

---

## Category 3: S3 Storage & Optimization ❌❌

### Question 5
A company stores archived data in S3 Glacier that is rarely accessed. When retrieval is needed, it must be available within 1-2 hours. They've been using Glacier Instant Retrieval but costs are too high. What should they change to?

A. S3 Glacier Deep Archive with expedited retrieval  
B. S3 Glacier Flexible Retrieval and stay within free quota  
C. S3 Standard-IA  
D. S3 Intelligent-Tiering

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**Glacier Flexible Retrieval** offers:
- **10 GB/month FREE retrieval quota**
- Retrieval times: Minutes to hours (fits 1-2 hour requirement)
- Lower storage cost than Instant Retrieval
- Perfect for rare, unpredictable access

**Cost Comparison:**
```
Storage Class          | Storage Cost | Retrieval Cost
──────────────────────────────────────────────────────
Instant Retrieval     | $0.004/GB    | $0.03/GB ❌
                      |              | Always charged
──────────────────────────────────────────────────────
Flexible Retrieval    | $0.0036/GB   | FREE (up to 10GB/month) ✅
                      |              | $0.01/GB after quota
──────────────────────────────────────────────────────
Deep Archive          | $0.00099/GB  | Retrieval: 12-48 hours ❌
                      |              | Too slow for requirement
```

**Strategy:**
1. Move data to Flexible Retrieval
2. Monitor monthly retrievals
3. Stay under 10 GB/month to avoid charges
4. Use standard retrieval (free tier eligible)

**Reference:** https://aws.amazon.com/s3/storage-classes/glacier/
</details>

---

### Question 6
An application requires object-level encryption before data leaves the application servers. The company wants AWS to manage the encryption keys securely. Which solution meets both requirements?

A. Enable SSE-S3 on the S3 bucket  
B. Use client-side encryption with KMS-managed customer master key (CMK)  
C. Enable SSE-KMS with AWS-managed keys  
D. Use pre-signed URLs with encryption headers

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
Requirements breakdown:
1. **Encrypt BEFORE upload** → Client-side encryption ✅
2. **AWS manages keys** → KMS-managed CMK ✅

**Encryption Methods:**
```
┌─────────────────────────────────────────────┐
│ SERVER-SIDE (Encrypt AFTER upload)         │
├─────────────────────────────────────────────┤
│ SSE-S3   │ AWS manages | ❌ Not client-side│
│ SSE-KMS  │ KMS manages | ❌ Not client-side│
│ SSE-C    │ Customer    | ❌ Not client-side│
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ CLIENT-SIDE (Encrypt BEFORE upload)         │
├─────────────────────────────────────────────┤
│ Client + │ Customer    | ❌ Customer manages│
│ Own Key  │ manages key |    (not AWS)      │
│          │             |                    │
│ Client + │ KMS manages | ✅ CORRECT         │
│ KMS CMK  │ Audit trail |    Both requirements│
└─────────────────────────────────────────────┘
```

**Implementation:**
```python
import boto3
from boto3.s3.transfer import S3Transfer

# Initialize KMS and S3 clients
kms = boto3.client('kms')
s3 = boto3.client('s3')

# Request data key from KMS
response = kms.generate_data_key(
    KeyId='arn:aws:kms:region:account:key/key-id',
    KeySpec='AES_256'
)

plaintext_key = response['Plaintext']
encrypted_key = response['CiphertextBlob']

# Encrypt data locally with plaintext key
encrypted_data = encrypt_with_key(data, plaintext_key)

# Upload encrypted data + encrypted key
s3.put_object(
    Bucket='bucket-name',
    Key='object-key',
    Body=encrypted_data,
    Metadata={
        'x-amz-matdesc': encrypted_key.decode('base64')
    }
)
```

**Reference:** https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingClientSideEncryption.html
</details>

---

## Category 4: IAM & Cross-Account Access ❌❌❌

### Question 7
A retail company needs to share S3 bucket logs with an external audit firm. The audit firm has its own AWS account. Bucket access is restricted by Service Control Policies (SCPs). What is the MOST secure approach?

A. Create IAM user in retail account and share credentials  
B. Modify SCP to allow external account access  
C. Add permissions in IAM policy, SCP, and bucket policy  
D. Create bucket policy granting audit account permissions with aws:SecureTransport condition

<details>
<summary>Show Answer</summary>

**Answer: D**

**Explanation:**
**Critical Understanding:**
- **SCPs CANNOT grant cross-account access**
- SCPs only DENY/restrict within YOUR organization
- Cross-account access requires bucket policies or IAM roles

**Why SCPs Don't Work:**
```
Service Control Policy (SCP):
├── Applies to: YOUR organization only
├── Function: DENY-based guardrails
├── Cannot: Grant permissions to external accounts
└── Scope: Organization management

Cross-Account Access Methods:
├── S3 Bucket Policy ✅
│   └── Can grant to external accounts
├── IAM Role Assumption ✅
│   └── External account assumes role
└── SCP ❌
    └── Doesn't work for external accounts
```

**Correct Bucket Policy:**
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
      "arn:aws:s3:::retail-logs",
      "arn:aws:s3:::retail-logs/*"
    ],
    "Condition": {
      "Bool": {
        "aws:SecureTransport": "true"
      }
    }
  }]
}
```

**Why aws:SecureTransport?**
- Ensures HTTPS-only access
- Prevents unencrypted transmission
- Security best practice

**Reference:** https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html
</details>

---

### Question 8
An AWS Lambda function needs to read messages from an SQS queue and write results to DynamoDB. What is the MOST secure way to grant these permissions?

A. Create resource-based policy on Lambda function  
B. Add inline policy to SQS queue  
C. Attach IAM execution role to Lambda with necessary permissions  
D. Use access keys in Lambda environment variables

<details>
<summary>Show Answer</summary>

**Answer: C**

**Explanation:**
**Lambda Permission Model:**

```
┌──────────────────────────────────────────┐
│ IAM EXECUTION ROLE (Identity-Based)      │
├──────────────────────────────────────────┤
│ What Lambda CAN DO                       │
│ Direction: Lambda → Other Services       │
│                                          │
│ Use for:                                 │
│ ✅ Reading from SQS                      │
│ ✅ Writing to DynamoDB                   │
│ ✅ Accessing KMS keys                    │
│ ✅ CloudWatch Logs                       │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│ RESOURCE POLICY (Resource-Based)         │
├──────────────────────────────────────────┤
│ WHO can invoke Lambda                    │
│ Direction: Other Services → Lambda       │
│                                          │
│ Use for:                                 │
│ ✅ API Gateway invoking Lambda           │
│ ✅ S3 triggering Lambda                  │
│ ✅ SNS invoking Lambda                   │
│ ✅ Cross-account invocations             │
└──────────────────────────────────────────┘
```

**Correct IAM Execution Role:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sqs:ReceiveMessage",
        "sqs:DeleteMessage",
        "sqs:GetQueueAttributes",
        "sqs:ChangeMessageVisibility"
      ],
      "Resource": "arn:aws:sqs:region:account:queue-name"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:UpdateItem"
      ],
      "Resource": "arn:aws:dynamodb:region:account:table/table-name"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "*"
    }
  ]
}
```

**Why others are wrong:**
- **A**: Resource policy is for WHO invokes Lambda, not WHAT Lambda accesses
- **B**: SQS queue policies don't grant Lambda permissions
- **D**: Never store credentials in environment variables (security risk)

**Reference:** https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html
</details>

---

## Category 5: Networking & VPC ❌❌

### Question 9
An EC2 instance in subnet A (10.0.1.0/24) needs to access a Transit Gateway in subnet B (10.0.2.0/24). Both subnets have different NACLs. What NACL rule evaluates traffic FROM the EC2 instance TO the Transit Gateway?

A. Inbound rule with source IP 10.0.1.0/24  
B. Outbound rule with source IP 10.0.1.0/24  
C. Outbound rule with destination IP 10.0.2.0/24  
D. Inbound rule with destination IP 10.0.2.0/24

<details>
<summary>Show Answer</summary>

**Answer: C**

**Explanation:**
**NACL Rule Evaluation Logic:**

```
┌────────────────────────────────────────┐
│ INBOUND Rules                          │
├────────────────────────────────────────┤
│ Evaluate: SOURCE IP address            │
│ Question: "WHERE is traffic FROM?"     │
│                                        │
│ Example:                               │
│ └─ Allow from 10.0.1.0/24             │
└────────────────────────────────────────┘

┌────────────────────────────────────────┐
│ OUTBOUND Rules ✅                      │
├────────────────────────────────────────┤
│ Evaluate: DESTINATION IP address       │
│ Question: "WHERE is traffic TO?"       │
│                                        │
│ Example:                               │
│ └─ Allow to 10.0.2.0/24 (TGW subnet)  │
└────────────────────────────────────────┘
```

**Memory Trick:**
```
INBOUND  = FROM (source)
OUTBOUND = TO (destination)

"OUT" = "TO where you're going OUT"
```

**Complete NACL Configuration:**
```
Subnet A NACL (10.0.1.0/24 - EC2 subnet):
┌────────────────────────────────────────┐
│ OUTBOUND Rules                         │
├────────────────────────────────────────┤
│ Rule │ Type       │ Destination    │ A│
│  100 │ All Traffic│ 10.0.2.0/24    │ A│ ✅
│  *   │ All Traffic│ 0.0.0.0/0      │ D│
└────────────────────────────────────────┘

Subnet B NACL (10.0.2.0/24 - TGW subnet):
┌────────────────────────────────────────┐
│ INBOUND Rules                          │
├────────────────────────────────────────┤
│ Rule │ Type       │ Source         │ A│
│  100 │ All Traffic│ 10.0.1.0/24    │ A│ ✅
│  *   │ All Traffic│ 0.0.0.0/0      │ D│
└────────────────────────────────────────┘
```

**Reference:** https://docs.aws.amazon.com/vpc/latest/tgw/tgw-nacls.html
</details>

---

### Question 10
A company wants EC2 instances in private subnets to download security patches from the internet. They want to avoid NAT Gateway costs. What should they use?

A. Internet Gateway attached to private subnets  
B. VPC peering to another VPC with internet access  
C. NAT instance on t3.micro (cheaper than NAT Gateway)  
D. They cannot avoid NAT Gateway for this requirement

<details>
<summary>Show Answer</summary>

**Answer: D**

**Explanation:**
For private subnet instances to reach the internet, you NEED network address translation (NAT).

**Internet Access from Private Subnet:**
```
┌─────────────────────────────────────────────┐
│ Options for Private Subnet Internet Access  │
├─────────────────────────────────────────────┤
│                                             │
│ NAT Gateway (AWS-Managed)                   │
│ ├─ Highly available                        │
│ ├─ No maintenance                          │
│ ├─ Scales automatically                    │
│ └─ Cost: ~$32/month + data transfer        │
│                                             │
│ NAT Instance (Self-Managed)                 │
│ ├─ Need to maintain                        │
│ ├─ Manual failover                         │
│ ├─ Performance limited by instance size    │
│ └─ Cost: EC2 + EBS + data transfer         │
│    (May be cheaper but more operational)   │
│                                             │
│ Internet Gateway                            │
│ └─ Only works with PUBLIC subnets ❌       │
│                                             │
│ VPC Peering                                 │
│ └─ Doesn't provide internet access ❌      │
└─────────────────────────────────────────────┘
```

**Why you can't avoid NAT:**
- Private subnet = No public IPs
- Internet requires public IPs for routing
- NAT translates private → public IPs
- This is the fundamental requirement

**Cost Optimization Strategies:**
```
1. Use VPC Endpoints for AWS services
   └─ Avoid NAT Gateway for S3, DynamoDB, etc.
   
2. Minimize internet-bound traffic
   └─ Use AWS services within VPC
   
3. Consolidate NAT Gateways
   └─ One NAT Gateway per AZ (not per subnet)
   
4. Consider NAT Instance for low-traffic scenarios
   └─ t3.micro ~$7/month (vs $32 for NAT Gateway)
   └─ But requires maintenance
```

**When NAT Gateway is mandatory:**
- Production workloads (HA required)
- High throughput needs
- No resources for NAT instance management

**Reference:** https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html
</details>

---

## Category 6: Aurora & RDS ❌

### Question 11
A company's Aurora cluster has experienced multiple disk failures resulting in corrupted data. They want to automatically protect against future disk failures without manual intervention. Which Aurora feature should they rely on?

A. Crash Recovery  
B. Storage Auto-Repair  
C. Survivable Cache  
D. Automated Backups

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**Aurora Reliability Features:**

```
┌──────────────────────────────────────────┐
│ Storage Auto-Repair ✅                   │
├──────────────────────────────────────────┤
│ Protects against: DISK FAILURE           │
│                                          │
│ How it works:                            │
│ • Data stored in 6 copies across 3 AZs  │
│ • Detects corrupted disk segments       │
│ • Automatically repairs from other copies│
│ • No data loss                           │
│ • No manual intervention                 │
│                                          │
│ Recovery Time: Seconds                   │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│ Crash Recovery                           │
├──────────────────────────────────────────┤
│ Protects against: DATABASE CRASH         │
│                                          │
│ How it works:                            │
│ • Parallel, asynchronous recovery       │
│ • Fast database restart                 │
│ • No binary log replay needed           │
│                                          │
│ Use Case: Process crash, not disk       │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│ Survivable Cache                         │
├──────────────────────────────────────────┤
│ Protects against: SLOW RECOVERY          │
│                                          │
│ How it works:                            │
│ • Page cache in separate process        │
│ • Survives database restart             │
│ • Reduces warm-up time                  │
│                                          │
│ Benefit: Performance, not data protection│
└──────────────────────────────────────────┘
```

**Storage Auto-Repair Process:**
```
1. Disk segment fails
   ↓
2. Aurora detects corruption
   ↓
3. Identifies which of 6 copies are affected
   ↓
4. Rebuilds segment from healthy copies
   ↓
5. Repairs completed (seconds)
   ↓
6. Application continues without impact
```

**Why others are wrong:**
- **A**: Crash recovery is for process crashes, not disk failures
- **C**: Survivable cache improves recovery speed, doesn't repair data
- **D**: Backups are for point-in-time recovery, not automatic repair

**Aurora Storage Architecture:**
```
Your Data
  ↓
Split into 10 GB segments
  ↓
Each segment replicated 6 ways
  ↓
Distributed across 3 AZs (2 copies per AZ)
  ↓
Can lose 2 copies without write impact
Can lose 3 copies without read impact
```

**Reference:** https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html
</details>

---

### Question 12
You're setting up IAM database authentication for Aurora MySQL. The following command fails. What's missing?

```sql
CREATE USER iam_db_user IDENTIFIED WITH AWSAuthenticationPlugin;
```

A. User must be created in RDS console first  
B. Need to add `AS 'RDS'` clause  
C. Must use `GRANT` instead of `CREATE USER`  
D. Plugin name should be `RDSAuthenticationPlugin`

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
The correct syntax requires the `AS 'RDS'` clause to specify the authentication method.

**Correct Syntax:**
```sql
CREATE USER iam_db_user 
IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';
```

**Complete IAM DB Authentication Setup:**
```sql
-- Step 1: Create IAM-authenticated user
CREATE USER iam_db_user 
IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';

-- Step 2: Grant necessary permissions
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO iam_db_user;
FLUSH PRIVILEGES;

-- Step 3: Verify user created
SELECT user, host, plugin FROM mysql.user 
WHERE user = 'iam_db_user';
```

**Application Connection:**
```python
import pymysql
import boto3

# Generate auth token
rds = boto3.client('rds')
token = rds.generate_db_auth_token(
    DBHostname='mydb.cluster-xxx.us-east-1.rds.amazonaws.com',
    Port=3306,
    DBUsername='iam_db_user',
    Region='us-east-1'
)

# Connect using token as password
connection = pymysql.connect(
    host='mydb.cluster-xxx.us-east-1.rds.amazonaws.com',
    user='iam_db_user',
    password=token,
    database='mydb',
    ssl={'ca': '/path/to/rds-ca-cert.pem'}
)
```

**IAM Policy Required:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "rds-db:connect",
    "Resource": "arn:aws:rds-db:region:account:dbuser:*/iam_db_user"
  }]
}
```

**Benefits of IAM DB Authentication:**
```
✅ No password storage needed
✅ Token expires after 15 minutes
✅ Uses IAM for access control
✅ Audit trail in CloudTrail
✅ Centralized credential management
❌ Additional latency for token generation
❌ Limited to 200 connections/sec
```

**Reference:** https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/UsingWithRDS.IAMDBAuth.html
</details>

---

## Category 7: API Gateway & CloudWatch ❌

### Question 13
A development team needs to debug API Gateway issues by viewing full request and response payloads including bodies. Which logging configuration should they enable?

A. CloudWatch Access Logging  
B. CloudWatch Execution Logging  
C. CloudTrail Data Events  
D. X-Ray Tracing

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**API Gateway Logging Types:**

```
┌────────────────────────────────────────────┐
│ ACCESS Logging (Metadata Only)             │
├────────────────────────────────────────────┤
│ Logs:                                      │
│ • Caller IP address                        │
│ • Request timestamp                        │
│ • HTTP method and resource path            │
│ • Status code                              │
│ • Response size                            │
│                                            │
│ Does NOT include:                          │
│ ❌ Request body                            │
│ ❌ Response body                           │
│ ❌ Integration details                     │
│                                            │
│ Use for: Access patterns, rate analysis   │
└────────────────────────────────────────────┘

┌────────────────────────────────────────────┐
│ EXECUTION Logging (Full Details) ✅        │
├────────────────────────────────────────────┤
│ Logs:                                      │
│ ✅ Request payload (body)                  │
│ ✅ Response payload (body)                 │
│ ✅ Integration latency                     │
│ ✅ Authorization results                   │
│ ✅ Error messages                          │
│ ✅ Request/response transformations       │
│                                            │
│ Levels:                                    │
│ • INFO: Basic execution flow              │
│ • ERROR: Errors only                      │
│                                            │
│ Use for: Debugging, troubleshooting       │
└────────────────────────────────────────────┘

┌────────────────────────────────────────────┐
│ CloudTrail (Management Operations)         │
├────────────────────────────────────────────┤
│ Logs:                                      │
│ • CreateRestApi                            │
│ • UpdateStage                              │
│ • DeleteResource                           │
│                                            │
│ Does NOT log: Application API calls ❌     │
│ Use for: Audit, compliance                │
└────────────────────────────────────────────┘
```

**Enable Execution Logging:**
```
Console:
API Gateway → Stages → Stage → Logs/Tracing
├─ Enable CloudWatch Logs: Yes
├─ Log level: INFO or ERROR
└─ Log full requests/responses data: Yes ✅

CloudFormation:
AWS::ApiGateway::Stage:
  Properties:
    MethodSettings:
      - LoggingLevel: INFO  # or ERROR
        DataTraceEnabled: true  # ✅ Includes payloads
```

**Example Execution Log:**
```
Execution log for request abc123:
Received request: POST /orders
Request body: {"productId":"P001","quantity":2}
Calling Lambda function: arn:aws:lambda:...
Lambda execution time: 543ms
Lambda response: {"orderId":"O123","status":"created"}
Sent response: 200 OK
Response body: {"orderId":"O123","status":"created"}
```

**Cost Consideration:**
```
Execution logging can be expensive:
├─ Each request generates multiple log entries
├─ Payloads can be large
└─ Use in non-production or time-limited

Best Practice:
├─ Enable for debugging
├─ Set to ERROR in production
└─ Use sampling if needed
```

**Reference:** https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-logging.html
</details>

---

### Question 14
An API Gateway REST API needs to securely access EC2-based microservices in a private subnet without exposing the backend to the internet. What should you implement?

A. Use security groups to allow only API Gateway IPs  
B. Create VPC endpoint and use private integration  
C. Place EC2 instances behind public ALB  
D. Use Lambda proxy integration

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**Private Integration Architecture:**

```
┌──────────────────────────────────────────┐
│ INTERNET                                 │
│                                          │
│ ┌────────────────────────────┐          │
│ │ API Gateway (Regional)     │          │
│ │ • Public endpoint          │          │
│ │ • Resource policies        │          │
│ └────────────────────────────┘          │
└──────────────────────────────────────────┘
              │
              │ AWS PrivateLink
              ▼
┌──────────────────────────────────────────┐
│ VPC                                      │
│                                          │
│ ┌────────────────────────────┐          │
│ │ VPC Endpoint (Interface)   │          │
│ │ • Private IP via ENI       │          │
│ │ • Security Group           │          │
│ └────────────────────────────┘          │
│              │                           │
│              ▼                           │
│ ┌────────────────────────────┐          │
│ │ Private Subnet             │          │
│ │ • EC2 microservices        │          │
│ │ • Internal ALB             │          │
│ └────────────────────────────┘          │
└──────────────────────────────────────────┘
```

**Why Public IP Whitelisting Fails:**
```
❌ Problem with API Gateway IPs:
   • IPs constantly change
   • Not documented by AWS
   • Managed service = No control
   • Security rules would break
   
✅ Solution: Private Integration
   • No public IPs involved
   • VPC endpoint provides private path
   • Security groups on VPC endpoint
   • Reliable and secure
```

**Configuration Steps:**

**1. Create VPC Endpoint:**
```bash
aws ec2 create-vpc-endpoint \
  --vpc-id vpc-xxx \
  --vpc-endpoint-type Interface \
  --service-name com.amazonaws.region.execute-api \
  --subnet-ids subnet-xxx subnet-yyy \
  --security-group-ids sg-xxx
```

**2. API Gateway VPC Link:**
```
API Gateway Console:
├─ VPC Links (under APIs)
├─ Create VPC Link
│   ├─ Name: my-vpc-link
│   └─ Target NLB: nlb-xxx
└─ Integration: VPC_LINK
    └─ VPC Link ID: vpclnk-xxx
```

**3. Security Groups:**
```
VPC Endpoint SG:
Inbound:
├─ Type: HTTPS (443)
├─ Source: 0.0.0.0/0
└─ Description: Accept API requests

EC2/ALB SG:
Inbound:
├─ Type: HTTP/HTTPS
├─ Source: sg-vpc-endpoint
└─ Description: Only from API Gateway
```

**Benefits:**
```
✅ Backend never exposed to internet
✅ No need to manage changing IPs
✅ Leverages AWS PrivateLink
✅ Centralized security controls
✅ Lower latency (AWS backbone)
```

**Reference:** https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-private-integration.html
</details>

---

## Category 8: EC2 & Storage ❌

### Question 15
An administrator attached a new EBS volume to an EC2 instance for additional data storage. After rebooting, the instance failed to start, with the OS attempting to mount the wrong volume as root. What is the MOST likely cause?

A. Wrong device name was used when attaching  
B. New volume has duplicate filesystem label  
C. Instance AMI was corrupted  
D. Volume encryption mismatch

<details>
<summary>Show Answer</summary>

**Answer: B**

**Explanation:**
**Root Cause: Duplicate Filesystem Labels**

```
┌─────────────────────────────────────────────┐
│ Problem Scenario                            │
├─────────────────────────────────────────────┤
│                                             │
│ Root Volume (/dev/xvda):                    │
│ ├─ Filesystem Label: "/"                   │
│ └─ Contains: OS files ✅                   │
│                                             │
│ Data Volume (/dev/xvdf):                    │
│ ├─ Filesystem Label: "/" ⚠️  (DUPLICATE!) │
│ └─ Contains: Application data ❌           │
│                                             │
│ On Boot:                                    │
│ └─ OS searches for label "/"               │
│     ├─ Finds TWO volumes                   │
│     ├─ Mounts first match (might be wrong) │
│     └─ Boots from data volume ❌           │
│         └─ Boot fails (no OS files)        │
└─────────────────────────────────────────────┘
```

**Diagnosis:**
```bash
# List all attached volumes
lsblk

# Check filesystem labels
sudo e2label /dev/xvda1
# Output: "/"

sudo e2label /dev/xvdf1
# Output: "/"  ← DUPLICATE FOUND!

# Check UUIDs (always unique)
sudo blkid
# /dev/xvda1: UUID="abc-123" LABEL="/"
# /dev/xvdf1: UUID="def-456" LABEL="/"
```

**Solution:**
```bash
# Step 1: Boot from recovery console or snapshot

# Step 2: Change data volume label
sudo e2label /dev/xvdf1 data-volume

# Step 3: Verify
sudo e2label /dev/xvdf1
# Output: "data-volume" ✅

# Step 4: Update /etc/fstab to use UUIDs
sudo nano /etc/fstab

# BEFORE (uses labels):
LABEL=/         /         ext4  defaults  0 0
LABEL=/data     /data     ext4  defaults  0 0

# AFTER (uses UUIDs):
UUID=abc-123    /         ext4  defaults  0 0
UUID=def-456    /data     ext4  defaults  0 0

# Step 5: Test
sudo mount -a  # Verify fstab entries
sudo reboot    # Safe to reboot now
```

**Prevention Best Practices:**

**1. Always Use UUIDs in /etc/fstab:**
```bash
# UUIDs are guaranteed unique
UUID=abc-123  /      ext4  defaults  0 0
UUID=def-456  /data  ext4  defaults  0 0

# NOT recommended (labels can duplicate):
LABEL=/       /      ext4  defaults  0 0
```

**2. Use Descriptive Unique Labels:**
```bash
# Good:
sudo e2label /dev/xvdf1 mysql-data
sudo e2label /dev/xvdg1 app-logs

# Bad:
sudo e2label /dev/xvdf1 /
sudo e2label /dev/xvdg1 /
```

**3. Verify Before Attaching:**
```bash
# Check new volume label before attaching
sudo e2label /dev/xvdf1
# Change if needed
sudo e2label /dev/xvdf1 unique-name
```

**4. Test in Non-Production First:**
```
Development:
├─ Attach volume
├─ Update fstab
├─ Reboot and verify
└─ Document UUID/label mapping

Production:
└─ Apply tested configuration
```

**Reference:** https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-booting-from-wrong-volume.html
</details>

---

## 📊 Progress Tracker

Track your improvement on these weak areas:

```
Topic                          | Initial | After Review | Target
───────────────────────────────────────────────────────────────
Amazon Polly Features          |   0%    |     %        |  100%
CloudFront Certificates        |   0%    |     %        |  100%
S3 Glacier Optimization        |   0%    |     %        |  100%
Cross-Account Access (S3)      |   0%    |     %        |  100%
Lambda IAM Roles               |   0%    |     %        |  100%
NACL Rule Evaluation           |   0%    |     %        |  100%
Aurora Reliability Features    |   0%    |     %        |  100%
Aurora IAM Authentication      |   0%    |     %        |  100%
API Gateway Logging            |   0%    |     %        |  100%
API Gateway Private Integration|   0%    |     %        |  100%
EC2 Filesystem Labels          |   0%    |     %        |  100%
Data Ownership in AWS          |   0%    |     %        |  100%
```

---

## 🎯 Next Steps

1. **Review all explanations** above carefully
2. **Practice hands-on** labs for weak areas
3. **Retake full practice test** in 2-3 days
4. **Target score**: 85%+ overall, 80%+ per domain

---

**Created**: March 2, 2026  
**Source**: Practice Test 2 Analysis (49/65 - 75.38%)  
**Focus**: Incorrect questions only

