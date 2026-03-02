# Practice Test 4 (SAA-C03) - Exam Review

**Date:** March 1, 2026  
**Score:** 49/65 (75.38%) - ⚠️ **BORDERLINE PASS**  
**Time Taken:** 98 minutes 56 seconds  
**Status:** Above passing threshold but needs improvement  
**Passing Score:** 72% (need 47/65 correct)

---

---

## 📊 Performance Summary

| Metric | Result |
|--------|--------|
| **Total Questions** | 65 |
| **Correct Answers** | 49 (75.38%) |
| **Incorrect Answers** | 16 (24.62%) |
| **Pass/Fail** | **BORDERLINE PASS** ⚠️ |
| **Passing Score** | 72% |
| **Gap from Test 3** | -4.62% (3 fewer correct) |

### Performance Trend
```
Test 1: 42/65 (64.62%) ❌ FAIL
Test 2: 49/65 (75.38%) ⚠️ BORDERLINE
Test 3: 52/65 (80.00%) ✅ PASS
Test 4: 49/65 (75.38%) ⚠️ BORDERLINE (-4.62%)
─────────────────────────────────────────────
Trend: ⚠️ Slight regression from Test 3
```

---

## 📈 Domain Performance Analysis

| Domain | Total | Correct | Incorrect | Score | Status |
|--------|-------|---------|-----------|-------|--------|
| **Design Secure Architectures** | 13 | 11 | 2 | 84.62% | ✅ Strong |
| **Design High-Performing Architectures** | 22 | 17 | 5 | 77.27% | ⚠️ Needs Review |
| **Design Resilient Architectures** | 19 | 14 | 5 | 73.68% | ⚠️ Needs Review |
| **Design Secure Applications** | 1 | 1 | 0 | 100.00% | ✅ Perfect |
| **Design Cost-Optimized Architectures** | 10 | 6 | 4 | 60.00% | ❌ **CRITICAL** |

### Domain Analysis

#### ✅ Strengths
- **Design Secure Architectures (84.62%)** - Strong understanding of security controls
- **Design High-Performing Architectures (77.27%)** - Good grasp of performance optimization

#### ⚠️ Areas Needing Improvement
- **Design Resilient Architectures (73.68%)** - Focus on fault tolerance and disaster recovery

#### ❌ Critical Weaknesses
- **Design Cost-Optimized Architectures (60.00%)** - **CRITICAL AREA**
  - EC2 pricing models (Reserved Instances, Savings Plans, Spot)

---

## ❌ Incorrect Questions - Detailed Review
   - Storage lifecycle policies
   - Cost allocation and billing

---

## ❌ Incorrect Questions - Detailed Review

---

### ❌ Question 7: Auto Scaling Metrics - Default vs Custom

**📋 COMPLETE QUESTION:**
A solutions architect is configuring an Auto Scaling group for a memory-intensive application. The application's performance degrades when memory utilization exceeds 80%. The architect wants to use target tracking scaling policy to maintain memory utilization around 70%. Which metric should be used?

**Options:**
A. CPU Utilization (predefined metric)
B. Network In (predefined metric)
C. Network Out (predefined metric)  
D. Memory Utilization (custom metric)

**Topic:** Design Resilient Architectures  
**Your Answer:** ❌ C. Network Out  
**Correct Answer:** ✅ **D. Memory Utilization (custom metric - requires CloudWatch agent)**

**🔍 DETAILED EXPLANATION:**

**EC2 Default vs Custom Metrics:**

```
┌────────────────────────────────────────────────────────┐
│         EC2 METRICS: DEFAULT vs CUSTOM                 │
├────────────────────────────────────────────────────────┤
│                                                         │
│  DEFAULT METRICS (No Agent Required) ✅                │
│  ┌──────────────────────────────────────┐              │
│  │  Automatically sent to CloudWatch:   │              │
│  │  ├─ CPU Utilization %                │              │
│  │  ├─ Network In (bytes)               │              │
│  │  ├─ Network Out (bytes)              │              │
│  │  ├─ Network Packets In               │              │
│  │  ├─ Network Packets Out              │              │
│  │  ├─ Disk Read Operations             │              │
│  │  ├─ Disk Write Operations            │              │
│  │  ├─ Disk Read Bytes                  │              │
│  │  ├─ Disk Write Bytes                 │              │
│  │  └─ Status Check Failed              │              │
│  │                                       │              │
│  │  ❌ NOT included:                     │              │
│  │     - Memory Utilization              │              │
│  │     - Disk Space Utilization          │              │
│  │     - Swap Usage                      │              │
│  └──────────────────────────────────────┘              │
│                                                         │
│  CUSTOM METRICS (Agent Required) ⚙️                    │
│  ┌──────────────────────────────────────┐              │
│  │  Install CloudWatch Agent to send:   │              │
│  │  ├─ Memory Utilization % ✅          │              │
│  │  ├─ Memory Used (MB)                 │              │
│  │  ├─ Memory Available (MB)            │              │
│  │  ├─ Disk Space % Used                │              │
│  │  ├─ Disk Space Free                  │              │
│  │  ├─ Swap Utilization %               │              │
│  │  └─ Application-specific metrics     │              │
│  │                                       │              │
│  │  Requires:                            │              │
│  │  1. CloudWatch Agent installation    │              │
│  │  2. IAM role with CloudWatch perms   │              │
│  │  3. Agent configuration file         │              │
│  └──────────────────────────────────────┘              │
│                                                         │
└────────────────────────────────────────────────────────┘
```

**Why Memory Is NOT a Predefined Metric:**

```
┌─────────────────────────────────────────┐
│  EC2 Instance                           │
│  ┌───────────────────────────────────┐  │
│  │   Operating System                │  │
│  │   ┌─────────────────────────────┐ │  │
│  │   │  Application                │ │  │
│  │   │  Memory Usage: 80%          │ │  │
│  │   └─────────────────────────────┘ │  │
│  │                                   │  │
│  │   OS reports memory to itself    │  │
│  │   ❌ AWS hypervisor cannot see    │  │
│  │      inside guest OS memory       │  │
│  └───────────────────────────────────┘  │
│                                          │
│  AWS Hypervisor View:                   │
│  ┌───────────────────────────────────┐  │
│  │  Can see:                         │  │
│  │  ✅ CPU usage                      │  │
│  │  ✅ Network traffic                │  │
│  │  ✅ Disk I/O                       │  │
│  │  ❌ Memory usage (inside guest)    │  │
│  └───────────────────────────────────┘  │
│                                          │
│  Solution: CloudWatch Agent             │
│  ┌───────────────────────────────────┐  │
│  │  Agent runs inside OS              │  │
│  │  Reads memory stats                │  │
│  │  Sends to CloudWatch               │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

**Auto Scaling with Custom Memory Metric:**

**Complete Setup Architecture:**

```
┌──────────────────────────────────────────────────────┐
│     AUTO SCALING WITH MEMORY METRIC                  │
├──────────────────────────────────────────────────────┤
│                                                       │
│  Step 1: Install CloudWatch Agent on EC2             │
│  ┌────────────────────────────────────┐              │
│  │  EC2 Instance (Launch Template)    │              │
│  │  ├─ UserData script:               │              │
│  │  │  #!/bin/bash                    │              │
│  │  │  wget https://s3.../agent.rpm   │              │
│  │  │  rpm -U ./agent.rpm             │              │
│  │  │  /opt/aws/cloudwatch/           │              │
│  │  │    amazon-cloudwatch-agent-ctl  │              │
│  │  │    -a fetch-config              │              │
│  │  │    -m ec2 -s                    │              │
│  │  └─ IAM Role:                      │              │
│  │     CloudWatchAgentServerPolicy    │              │
│  └────────────────────────────────────┘              │
│           │                                           │
│           │ Sends metrics every 60 sec                │
│           ▼                                           │
│  Step 2: CloudWatch Custom Metric                    │
│  ┌────────────────────────────────────┐              │
│  │  Metric Name:                      │              │
│  │    mem_used_percent                │              │
│  │  Namespace:                        │              │
│  │    CWAgent                         │              │
│  │  Dimensions:                       │              │
│  │    InstanceId: i-1234567890abcdef0 │              │
│  └────────────────────────────────────┘              │
│           │                                           │
│           │ Used by Auto Scaling                      │
│           ▼                                           │
│  Step 3: Target Tracking Scaling Policy              │
│  ┌────────────────────────────────────┐              │
│  │  PolicyType: TargetTrackingScaling │              │
│  │  TargetValue: 70                   │              │
│  │  CustomizedMetricSpecification:    │              │
│  │    MetricName: mem_used_percent    │              │
│  │    Namespace: CWAgent              │              │
│  │    Statistic: Average              │              │
│  └────────────────────────────────────┘              │
│           │                                           │
│           │ Triggers scaling                          │
│           ▼                                           │
│  Step 4: Auto Scaling Group                          │
│  ┌────────────────────────────────────┐              │
│  │  Current: 2 instances              │              │
│  │  Desired: 2                        │              │
│  │  Min: 1, Max: 10                   │              │
│  │                                    │              │
│  │  When mem > 70%: Scale OUT         │              │
│  │  When mem < 70%: Scale IN          │              │
│  └────────────────────────────────────┘              │
│                                                       │
└──────────────────────────────────────────────────────┘
```

**CloudWatch Agent Configuration File:**

```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "cwagent"
  },
  "metrics": {
    "namespace": "CWAgent",
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
      },
      "disk": {
        "measurement": [
          {
            "name": "used_percent",
            "rename": "DiskUtilization",
            "unit": "Percent"
          }
        ],
        "metrics_collection_interval": 60,
        "resources": [
          "/"
        ]
      }
    }
  }
}
```

**Auto Scaling Policy (JSON):**

```json
{
  "TargetTrackingScalingPolicyConfiguration": {
    "TargetValue": 70.0,
    "CustomizedMetricSpecification": {
      "MetricName": "mem_used_percent",
      "Namespace": "CWAgent",
      "Statistic": "Average",
      "Dimensions": [
        {
          "Name": "AutoScalingGroupName",
          "Value": "my-asg"
        }
      ]
    },
    "ScaleOutCooldown": 300,
    "ScaleInCooldown": 300
  }
}
```

**Predefined Metrics for Auto Scaling:**

| Metric | Type | Agent Required? | Use Case |
|--------|------|----------------|----------|
| **ASGAverageCPUUtilization** | Predefined | ❌ No | CPU-intensive apps |
| **ASGAverageNetworkIn** | Predefined | ❌ No | Network receive bottleneck |
| **ASGAverageNetworkOut** | Predefined | ❌ No | Network send bottleneck |
| **ALBRequestCountPerTarget** | Predefined | ❌ No | Web applications |
| **Memory Utilization** | Custom | ✅ YES | Memory-intensive apps |
| **Disk Utilization** | Custom | ✅ YES | Storage-intensive apps |

**Installation Commands:**

**Amazon Linux 2:**
```bash
# Download and install agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
sudo rpm -U ./amazon-cloudwatch-agent.rpm

# Create config file (save above JSON to /opt/aws/amazon-cloudwatch-agent/etc/config.json)

# Start agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/config.json
```

**Ubuntu:**
```bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -s \
  -c file:/opt/aws/amazon-cloudwatch-agent/etc/config.json
```

**IAM Role Policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:PutMetricData",
        "ec2:DescribeVolumes",
        "ec2:DescribeTags",
        "logs:PutLogEvents",
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:DescribeLogStreams"
      ],
      "Resource": "*"
    }
  ]
}
```

**Scaling Behavior Example:**

```
Scenario: 2 instances, each with 4 GB RAM

Time: 10:00 AM
├─ Instance 1: 60% memory (2.4 GB used)
├─ Instance 2: 65% memory (2.6 GB used)
├─ Average: 62.5%
└─ Action: None (below 70% target)

Time: 11:00 AM (traffic spike)
├─ Instance 1: 75% memory (3.0 GB used)
├─ Instance 2: 80% memory (3.2 GB used)
├─ Average: 77.5%
└─ Action: Scale OUT - launch instance 3 ✅

Time: 11:05 AM (instance 3 launching)
├─ Instance 1: 75% memory
├─ Instance 2: 80% memory
├─ Instance 3: Initializing...
└─ Action: Wait for instance 3 to be healthy

Time: 11:10 AM (traffic distributed)
├─ Instance 1: 55% memory
├─ Instance 2: 60% memory
├─ Instance 3: 50% memory
├─ Average: 55%
└─ Action: None (below 70% target) ✅

Time: 3:00 PM (traffic decreased)
├─ Instance 1: 40% memory
├─ Instance 2: 45% memory
├─ Instance 3: 35% memory
├─ Average: 40%
└─ Action: Scale IN - terminate 1 instance ✅
```

**🎯 KEY TAKEAWAYS:**
- ✅ **Memory Utilization is NOT a predefined EC2 metric**
- ✅ Must install CloudWatch Agent to collect memory metrics
- ✅ Agent reads OS-level memory stats and sends to CloudWatch
- ✅ Default metrics: CPU, Network, Disk I/O (no agent needed)
- ✅ Custom metrics: Memory, Disk space, Swap (agent required)
- ✅ IAM role needed: CloudWatchAgentServerPolicy
- ❌ Cannot use memory for target tracking without agent

**💡 MEMORY AID:** "MDS = Memory, Disk, Swap (need Agent), CPU/Network = Built-in"

---

### ❌ Question 13: Redshift Snapshot Costs

**📋 COMPLETE QUESTION:**
A data analytics company uses Amazon Redshift for their data warehouse. The monthly AWS bill shows unexpected high storage costs for Redshift snapshots. The company has:
- Automated daily snapshots (retention: 7 days)
- 50+ manual snapshots from the past 2 years
- Active cluster size: 5 TB

Which action will MOST effectively reduce Redshift snapshot storage costs?

**Options:**
A. Increase automated snapshot retention to 35 days for better data protection
B. Delete unneeded manual snapshots from previous years
C. Enable cross-region snapshot copy for disaster recovery
D. Upgrade to a larger Redshift cluster for better performance

**Topic:** Design Cost-Optimized Architectures  
**Your Answer:** ❌ A. Increase automated snapshot retention to 35 days  
**Correct Answer:** ✅ **B. Delete unneeded manual snapshots**

**🔍 DETAILED EXPLANATION:**

**Redshift Snapshot Types:**

```
┌────────────────────────────────────────────────────────┐
│           REDSHIFT SNAPSHOT TYPES                      │
├────────────────────────────────────────────────────────┤
│                                                         │
│  AUTOMATED SNAPSHOTS ⏰                                │
│  ┌──────────────────────────────────────┐              │
│  │  Retention: 1-35 days (configurable) │              │
│  │  Frequency: 8 hours OR 5 GB changed  │              │
│  │  Auto-deleted: YES ✅                 │              │
│  │  Cost: Included (1x cluster size)    │              │
│  │  Lifecycle: Automatic management     │              │
│  │                                      │              │
│  │  Day 1: Snapshot-auto-2024-03-01    │              │
│  │  Day 2: Snapshot-auto-2024-03-02    │              │
│  │  Day 3: Snapshot-auto-2024-03-03    │              │
│  │  ...                                 │              │
│  │  Day 7: Snapshot-auto-2024-03-07    │              │
│  │  Day 8: Delete Day 1 snapshot ✅     │              │
│  └──────────────────────────────────────┘              │
│                                                         │
│  MANUAL SNAPSHOTS 🔧                                   │
│  ┌──────────────────────────────────────┐              │
│  │  Retention: INDEFINITE ⚠️            │              │
│  │  Frequency: On-demand (user trigger) │              │
│  │  Auto-deleted: NO ❌                  │              │
│  │  Cost: $0.024/GB/month               │              │
│  │  Lifecycle: Manual management        │              │
│  │                                      │              │
│  │  Snapshot-before-upgrade-2024-01    │              │
│  │  Snapshot-before-upgrade-2024-02    │              │
│  │  Snapshot-before-upgrade-2024-03    │              │
│  │  ...                                 │              │
│  │  Snapshot-before-upgrade-2026-03    │              │
│  │  ⚠️ All 50 retained FOREVER          │              │
│  │  ⚠️ Accumulating costs               │              │
│  └──────────────────────────────────────┘              │
│                                                         │
└────────────────────────────────────────────────────────┘
```

**Cost Calculation:**

**Current Situation:**
```
┌─────────────────────────────────────────────────────┐
│  CURRENT SNAPSHOT COSTS                             │
├─────────────────────────────────────────────────────┤
│                                                      │
│  Automated Snapshots (7 days):                      │
│  ├─ Retention: 7 days                               │
│  ├─ Storage: Up to 1x cluster size (5 TB)          │
│  ├─ Cost: FREE (included with cluster)              │
│  └─ Monthly cost: $0                                │
│                                                      │
│  Manual Snapshots (50 snapshots):                   │
│  ├─ Average size: 5 TB each (incremental backup)   │
│  │   First snapshot: 5 TB (full)                    │
│  │   Subsequent: ~500 GB each (changes only)        │
│  ├─ Total storage: ~30 TB                           │
│  │   (5 TB full + 49 × 500 GB incremental)         │
│  ├─ Cost: $0.024/GB/month                          │
│  └─ Monthly cost: 30,000 GB × $0.024 = $720/month  │
│                                                      │
│  TOTAL SNAPSHOT COST: $720/month ⚠️                 │
│                                                      │
└─────────────────────────────────────────────────────┘
```

**After Deleting 45 Old Manual Snapshots:**
```
┌─────────────────────────────────────────────────────┐
│  OPTIMIZED SNAPSHOT COSTS                           │
├─────────────────────────────────────────────────────┤
│                                                      │
│  Automated Snapshots (7 days):                      │
│  └─ Monthly cost: $0 (no change)                    │
│                                                      │
│  Manual Snapshots (5 snapshots - keep recent):     │
│  ├─ Total storage: ~7 TB                            │
│  │   (5 TB full + 4 × 500 GB incremental)          │
│  ├─ Cost: $0.024/GB/month                          │
│  └─ Monthly cost: 7,000 GB × $0.024 = $168/month   │
│                                                      │
│  TOTAL SNAPSHOT COST: $168/month ✅                 │
│  SAVINGS: $552/month ($6,624/year) 💰              │
│                                                      │
└─────────────────────────────────────────────────────┘
```

**Why Other Options Are Wrong:**

**❌ Option A: Increase retention to 35 days**
```
Current automated retention: 7 days → FREE
Increased retention: 35 days

Impact:
├─ More snapshots retained: 7 → 35
├─ Storage: 5 TB → 25 TB (5x increase)
├─ Cost: $0 → $0 (still FREE up to 1x cluster size)
│   But beyond 1x, charged $0.024/GB
├─ Beyond 5 TB: (25 TB - 5 TB) × 1024 × $0.024
│   = 20,480 GB × $0.024 = $491/month additional
└─ Result: INCREASES cost, doesn't reduce ❌

Note: First 100% of cluster size in automated 
snapshots is FREE. Additional storage is charged.
```

**❌ Option C: Cross-region snapshot copy**
```
Enable cross-region copy:

Impact:
├─ Creates copy of snapshots in another region
├─ Storage doubled (primary + secondary region)
├─ Data transfer: $0.02/GB out of source region
├─ Storage cost in second region: $0.024/GB/month
├─ For 30 TB snapshots:
│   Transfer: 30,000 GB × $0.02 = $600 (one-time)
│   Storage: 30,000 GB × $0.024 = $720/month ongoing
└─ Result: DOUBLES cost ($1,440/month) ❌
```

**❌ Option D: Upgrade cluster**
```
Upgrade cluster size: 5 TB → 10 TB

Impact:
├─ Cluster cost increases (2x nodes or larger nodes)
├─ Snapshot storage unchanged
├─ Does not address snapshot retention issue
└─ Result: Increases costs, doesn't help snapshots ❌
```

**Snapshot Management Best Practices:**

```
┌────────────────────────────────────────────────────┐
│     REDSHIFT SNAPSHOT BEST PRACTICES               │
├────────────────────────────────────────────────────┤
│                                                     │
│  1. Automated Snapshots (Daily Operations)         │
│     ├─ Keep retention reasonable (7-14 days)       │
│     ├─ Automatically deleted after retention       │
│     └─ Use for short-term recovery                 │
│                                                     │
│  2. Manual Snapshots (Special Events)              │
│     ├─ Before major upgrades ✅                     │
│     ├─ Before schema changes ✅                     │
│     ├─ Month-end/quarter-end for compliance ✅     │
│     └─ Delete after event + grace period           │
│                                                     │
│  3. Audit and Cleanup                              │
│     ├─ Monthly review of manual snapshots          │
│     ├─ Delete snapshots > 90 days old              │
│     ├─ Keep only compliance-required snapshots     │
│     └─ Document retention policy                   │
│                                                     │
│  4. Cross-Region (Disaster Recovery Only)          │
│     ├─ Enable only if required for DR              │
│     ├─ Copy only critical snapshots                │
│     └─ Same retention policy in both regions       │
│                                                     │
└────────────────────────────────────────────────────┘
```

**Automated Cleanup Script (AWS CLI):**

```bash
#!/bin/bash
# Delete Redshift manual snapshots older than 90 days

CLUSTER_ID="my-redshift-cluster"
RETENTION_DAYS=90
CUTOFF_DATE=$(date -d "$RETENTION_DAYS days ago" +%Y-%m-%d)

# List all manual snapshots
aws redshift describe-cluster-snapshots \
  --cluster-identifier $CLUSTER_ID \
  --snapshot-type manual \
  --query "Snapshots[?SnapshotCreateTime<='$CUTOFF_DATE'].SnapshotIdentifier" \
  --output text | while read snapshot; do
  
  echo "Deleting snapshot: $snapshot"
  aws redshift delete-cluster-snapshot \
    --snapshot-identifier $snapshot
done
```

**AWS Console Steps:**

```
1. Navigate to Amazon Redshift Console
   └─ Select "Snapshots" from left menu

2. Filter Manual Snapshots
   ├─ Snapshot type: Manual
   ├─ Sort by: Creation date (oldest first)
   └─ Review snapshots older than 90 days

3. Select Snapshots for Deletion
   ├─ Check boxes next to old snapshots
   ├─ Verify no longer needed
   └─ Consider business/compliance requirements

4. Delete Snapshots
   ├─ Click "Delete snapshot"
   ├─ Confirm deletion (cannot be undone)
   └─ Repeat for all unnecessary snapshots

5. Verify Cost Reduction
   ├─ Check Cost Explorer after 24-48 hours
   └─ Monitor Redshift snapshot storage metrics
```

**Snapshot Retention Policy Template:**

```yaml
RedshiftSnapshotPolicy:
  AutomatedSnapshots:
    Retention: 7 days
    Frequency: Every 8 hours
    Purpose: Daily operations recovery
    
  ManualSnapshots:
    PreUpgrade:
      Retention: 30 days after upgrade
      Delete: After successful upgrade validation
      
    MonthEnd:
      Retention: 13 months (1 year + current)
      Purpose: Compliance, financial reporting
      
    QuarterEnd:
      Retention: 7 years
      Purpose: Legal/regulatory compliance
      
    AdHoc:
      Retention: 90 days maximum
      Delete: After purpose fulfilled
      
  CrossRegion:
    Enabled: Only for production cluster
    Retention: Match primary region
    Purpose: Disaster recovery only
```

**🎯 KEY TAKEAWAYS:**
- ✅ **Manual snapshots are retained INDEFINITELY** until explicitly deleted
- ✅ Automated snapshots auto-delete after retention period (FREE up to 1x cluster size)
- ✅ Deleting old manual snapshots is the FASTEST way to reduce costs
- ✅ Redshift snapshot storage: $0.024/GB/month
- ✅ Review and delete manual snapshots regularly (monthly audit)
- ❌ Increasing automated retention beyond cluster size INCREASES cost
- ❌ Cross-region copy DOUBLES snapshot storage costs
- ❌ Cluster upgrades don't reduce snapshot costs

**💡 MEMORY AID:** "Manual = Must-delete (never auto-deletes), Auto = Auto-expires"

**Exam Keywords:**
- "Reduce Redshift snapshot costs" → Delete old manual snapshots ✅
- "Manual snapshots accumulating" → Indefinite retention issue ✅
- "Unexpected snapshot charges" → Review manual snapshot count ✅

---  
**Your Answer:** Expedited  
**Correct Answer:** Standard  

**Why You Got It Wrong:**
- Expedited (1-5 min) is faster but more expensive
- Standard (3-5 hours) meets the requirement and is cost-effective
- Bulk (5-12 hours) is too slow

**Key Takeaway:**
> ⏱️ **S3 Glacier Flexible Retrieval tiers: Expedited (1-5min, $$), Standard (3-5hr, $), Bulk (5-12hr, ¢)**

---

### Question 18: Backend Security Group ❌
**Topic:** Design Resilient Architectures  
**Your Answer:** Use the launch configuration as the source  
**Correct Answer:** Use the frontend security group ID as the source  

**Why You Got It Wrong:**
- Security group rules accept other SG IDs as sources
- Launch configurations cannot be used in SG rules
- SG-to-SG references automatically handle IP changes

**Key Takeaway:**
> 🔒 **Security groups can reference other security groups. This provides automatic, scalable access control.**

---

### Question 19: Multi-Tier Savings Plans ❌
**Topic:** Design Cost-Optimized Architectures  
**Your Answer:** EC2 Instance SP for web; Compute SP for app and DB  
**Correct Answer:** Compute SP for web; EC2 Instance SP for app; RDS RI for DB  

**Why You Got It Wrong:**
- Web tier (varied families) needs Compute Savings Plan flexibility
- App tier (stable family) benefits from higher EC2 Instance SP discount
- RDS requires its own Reserved Instances, not covered by EC2 SPs

**Key Takeaway:**
> 💡 **Compute SP = flexibility; EC2 Instance SP = highest discount; RDS = separate RIs**

---

### Question 25: CloudTrail Log Integrity ❌
**Topic:** Design Secure Architectures  
**Your Answer:** Use SSE-KMS encryption for the CloudTrail logs  
**Correct Answer:** Enable CloudTrail log file integrity validation  

**Why You Got It Wrong:**
- Encryption protects confidentiality, not integrity
- Log file integrity validation uses SHA-256 hashes + digital signatures
- Provides cryptographic proof logs weren't tampered with

**Key Takeaway:**
> 🔐 **CloudTrail log file integrity validation = tamper detection. Encryption = confidentiality.**

---

### Question 28: Auto Scaling Lifecycle ❌
**Topic:** Design High-Performing Architectures  
**Your Answer:** Customize the termination policy to copy data  
**Correct Answer:** Add lifecycle hooks to the Auto Scaling group  

**Why You Got It Wrong:**
- Termination policies decide WHICH instance to terminate
- Lifecycle hooks allow custom actions BEFORE termination
- Hooks provide wait state for cleanup scripts/data copy

**Key Takeaway:**
> ⏸️ **Lifecycle hooks = pause before termination. Termination policies = choose which instance.**

---

### Question 34: Mixed Instance Auto Scaling ❌
**Topic:** Design Cost-Optimized Architectures  
**Your Answer:** Use all t2.micro On-Demand instances  
**Correct Answer:** Use mixed instances policy: On-Demand base = 2; above that 20% On-Demand / 80% Spot  

**Why You Got It Wrong:**
- Downgrading to t2.micro may hurt performance
- Mixed instances policy balances cost and reliability
- Keep base capacity On-Demand, use Spot for burst

**Key Takeaway:**
> 🎯 **Mixed instances policy: stable base (On-Demand) + cost-effective burst (Spot)**

---

### Question 36: CloudFormation Drift Detection ❌
**Topic:** Design High-Performing Architectures  
**Your Answer:** Grant additional Read permissions to drift detection  
**Correct Answer:** Explicitly set the property values in the template  

**Why You Got It Wrong:**
- Drift detection only tracks explicitly declared properties
- Default values are not monitored
- Must declare all properties to detect drift

**Key Takeaway:**
> 📝 **CloudFormation drift detection: if not in template, it's not tracked. Declare defaults explicitly.**

---

### Question 38: Multi-Account ALB Access ❌
**Topic:** Design High-Performing Architectures  
**Your Answer:** B and D (missing C)  
**Correct Answer:** B and C  

**Why You Got It Wrong:**
- Both inbound rule for parent objects AND disabling Block Public Access are needed
- Missed the critical step of allowing public reads via bucket policy

**Key Takeaway:**
> 🌐 **S3 public website: (1) Disable Block Public Access + (2) Bucket policy for public reads**

---

### Question 45: Session Storage ❌
**Topic:** Design High-Performing Architectures  
**Your Answer:** C and D (ELB + ElastiCache)  
**Correct Answer:** B and D (DynamoDB + ElastiCache)  

**Why You Got It Wrong:**
- ELB does not store session data, only provides sticky sessions
- DynamoDB is a proper session store with TTL support
- ElastiCache is correct for in-memory caching

**Key Takeaway:**
> 💾 **Session stores: DynamoDB (durable, TTL) + ElastiCache (fast, in-memory)**

---

### Question 48: ALB Access Logging ❌
**Topic:** Design Secure Architectures  
**Your Answer:** AWS CloudTrail data events for the ALB  
**Correct Answer:** ALB access logs to Amazon S3  

**Why You Got It Wrong:**
- CloudTrail logs control plane API calls, not data plane requests
- ALB access logs record every request with client IP details
- Access logs are designed for forensics and compliance

**Key Takeaway:**
> 📊 **CloudTrail = control plane (API calls). ALB access logs = data plane (requests).**

---

### Question 56: EBS Cross-Region ❌
**Topic:** Design Resilient Architectures  
**Your Answer:** Enable Amazon S3 CRR on the snapshot storage bucket  
**Correct Answer:** Copy the EBS snapshot from us-east-1 to ap-south-1  

**Why You Got It Wrong:**
- Snapshots are stored in AWS-managed S3, you can't configure CRR
- Must use built-in snapshot copy feature
- Can copy across regions with optional encryption changes

**Key Takeaway:**
> 📸 **EBS snapshots: use snapshot copy API, not S3 replication**

---

### Question 58: Restore S3 Versioned Object ❌
**Topic:** Design Resilient Architectures  
**Your Answer:** Option D (partial understanding)  
**Correct Answer:** Delete the delete marker  

**Why You Got It Wrong:**
- Deleting with versioning creates a delete marker
- Must remove the delete marker to restore visibility
- Simply retrieving version ID doesn't restore normal access

**Key Takeaway:**
> 🗑️ **S3 versioning: delete = add marker. Restore = delete the marker.**

---

---

## 📚 Study Recommendations

### Priority 1: Cost Optimization (60% - CRITICAL)
**Time:** 3-4 hours

1. **EC2 Pricing Models**
   - Review: Reserved Instances vs Savings Plans
   - Module: `13-Cost-Optimization/README.md`
   - Practice: Cost calculator scenarios

2. **Storage Lifecycle & Glacier**
   - Understand: Standard → Standard-IA → Glacier tiers
   - Review: Glacier retrieval options (Expedited, Standard, Bulk)
   - Module: `04-Storage/README.md`

3. **Redshift Snapshots**
   - Manual vs automated snapshots
   - Snapshot retention and cleanup
   - Module: `11-Analytics/README.md`

### Priority 2: Auto Scaling & Resilience (73.68%)
**Time:** 2-3 hours

1. **Auto Scaling Metrics**
   - Default vs custom metrics
   - CloudWatch agent for memory/disk metrics
   - Module: `03-Compute/README.md`

2. **Lifecycle Hooks**
   - Termination policies vs lifecycle hooks
   - Use cases for graceful shutdown
   - Module: `12-Architecture-Patterns/README.md`

3. **Security Group Referencing**
   - SG-to-SG rules
   - Automatic scaling benefits
   - Module: `06-Networking/README.md`

### Priority 3: CloudFormation & Drift
**Time:** 1-2 hours

1. **Drift Detection**
   - Explicit vs implicit properties
   - Best practices for templates
   - Module: `12-Architecture-Patterns/README.md`

### Priority 4: CloudTrail vs Service Logs
**Time:** 1 hour

1. **Logging Comparison**
   - CloudTrail (control plane)
   - ALB access logs (data plane)
   - VPC Flow Logs (network layer)
   - Module: `09-Monitoring/README.md`

---

## 🎯 Quick Reference Cards

### Cost Optimization Cheat Sheet

```
EC2 PRICING:
├─ On-Demand: Pay per second, no commitment
├─ Reserved Instances: 1-3 year commit, specific family/size
├─ Savings Plans
│  ├─ Compute SP: Flexible (family/size/region), lower discount
│  └─ EC2 Instance SP: Fixed family, highest discount
└─ Spot: Up to 90% off, can be interrupted

GLACIER RETRIEVAL:
├─ Expedited: 1-5 minutes, $$$
├─ Standard: 3-5 hours, $
└─ Bulk: 5-12 hours, ¢

REDSHIFT SNAPSHOTS:
├─ Automated: Retained per policy, auto-deleted
└─ Manual: Persist forever, must delete manually ⚠️
```

### Security & Logging

```
CLOUDTRAIL LOG INTEGRITY:
├─ Enable: Log file integrity validation
├─ Creates: SHA-256 hashes + digital signatures
└─ Proves: Logs not tampered/deleted

LOGGING TYPES:
├─ CloudTrail: Control plane (CreateBucket, ModifyDB)
├─ ALB Access Logs: Data plane (HTTP requests, client IPs)
├─ VPC Flow Logs: Network (IP traffic, 5-tuple)
└─ S3 Access Logs: Bucket requests

SECURITY GROUP RULES:
├─ Can reference: Other SG IDs (auto-scales)
├─ Cannot reference: Launch configs, ASG names
└─ Stateful: Return traffic auto-allowed
```

### Auto Scaling & Resilience

```
AUTO SCALING METRICS:
├─ Predefined: CPU, Network In/Out, ALB RequestCount
└─ Custom: Memory, Disk (needs CloudWatch agent) ⚠️

LIFECYCLE HOOKS:
├─ Purpose: Run custom actions before termination
├─ States: Terminating:Wait, Launching:Wait
└─ Use cases: Backup data, drain connections

TERMINATION POLICIES:
├─ Purpose: Choose WHICH instance to terminate
├─ Types: OldestInstance, NewestInstance, ClosestToNextInstanceHour
└─ Not for: Running custom cleanup scripts ⚠️
```

---

## 📊 Progress Tracking

### Test Comparison

| Test | Score | Change | Resilient | High-Perf | Secure | Cost |
|------|-------|--------|-----------|-----------|--------|------|
| Test 1 | 64.62% | - | 71% | 43% | 78% | 100% |
| Test 2 | 75.38% | +10.76% | 87% | 75% | 56% | 86% |
| Test 3 | 80.00% | +4.62% | 81% | 88% | 78% | 63% |
| Test 4 | 75.38% | -4.62% | 74% | 77% | 85% | 60% |

### Weak Area Consistency

**Cost Optimization** has been consistently weak:
- Test 1: 100% (only 3 questions)
- Test 2: 86%
- Test 3: 63%
- Test 4: 60% ⚠️ **Getting worse!**

**Action:** Dedicate focused study session to cost optimization concepts.

---

## 🎯 Action Plan for Test 5

### Before Next Test (Target: 85%+)

1. ✅ **Complete cost optimization module** (4 hours)
   - [ ] Review pricing models
   - [ ] Practice cost calculator
   - [ ] Understand Glacier tiers

2. ✅ **Review auto scaling deep dive** (2 hours)
   - [ ] Lifecycle hooks vs termination policies
   - [ ] Custom metrics setup

3. ✅ **CloudFormation drift detection** (1 hour)
   - [ ] Template best practices

4. ✅ **Take focused quiz on weak areas** (14-Practice folder)

### During Test 5

- ⏰ Time management: ~2 minutes per question
- 🔍 Read EVERY word carefully (especially "MOST", "LEAST")
- 📌 Flag uncertain questions for review
- ✅ Focus on eliminating wrong answers first

---

## 📝 Notes

### Patterns Observed
1. **Cost questions consistently missed** - Need dedicated review
2. **Confusion between similar services** - Create comparison tables
3. **Missing "explicit" requirements** - CloudFormation defaults issue

### Test-Taking Issues
1. Rushed through cost questions (need to slow down)
2. Misread "control plane vs data plane" logging question
3. Overlooked security group referencing capability

---

**Next Steps:**
1. Review this document thoroughly
2. Focus on Priority 1 & 2 study areas
3. Take focused practice questions on weak domains
4. Schedule Test 5 after completing review (target: 1 week)

**Target for Test 5:** 55+/65 (85%+) ✅

---

[← Back to Exam Reviews](README.md) | [Main Guide](../README.md)

