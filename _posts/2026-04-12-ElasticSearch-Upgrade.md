---
title: Elastic Upgrade
date: 2026-04-12
categories: [Elastic Search (ELK) , Elastic Search , Kibana]
tags: [elastic,elk,elastic search,kibana,elastic upgrade]
---

## Overview / Introduction

Elasticsearch upgrades are critical operational tasks that must be planned carefully to prevent data loss, service downtime, and compatibility issues. This document covers the essential prerequisites, strategic planning, and step-by-step procedures for upgrading Elasticsearch clusters safely in production environments.

The fundamental goal is to upgrade your cluster with zero downtime by following a methodical, node-by-node approach while respecting compatibility matrices and infrastructure limitations.

---

## Important Concepts & Definitions

### **Version Terminology**

- **N (Current Version)**: The latest released version of Elasticsearch (e.g., 9.2.4)
- **N-1 (Recommended for Production)**: The previous minor version (e.g., if current is 9.2.4, upgrade to 9.2.3)
- **N-2 (Conservative Approach)**: Two versions back; some organizations require this for additional stability testing
- **Why Not Jump to Latest?**: New versions receive minimal real-world testing immediately after release. Organizations need time to identify and patch bugs before production use.

### **Upgrade Types**

- **Minor Upgrade**: Changes within the same major version (e.g., 8.19.0 → 8.19.9). Lower risk, fewer breaking changes.
- **Major Upgrade**: Changes to the major version number (e.g., 8.19.9 → 9.2.3). Significant risk; requires extensive testing and careful planning.

### **Node Roles & Data Tiers (ILM Context)**

- **Data Tiers** (in order of upgrade priority - least impactful first):
    - **Frozen Tier**: Oldest/archived data; minimal access; upgrade first
    - **Cold Tier**: Infrequently accessed data; second priority
    - **Warm Tier**: Moderately accessed data; third priority
    - **Hot Tier**: Active/current data; upgrade last among data nodes
- **Other Node Roles**:
    - **Master Eligible Node**: Can become cluster master; coordinate upgrade carefully
    - **Coordinating Node**: Routes requests; upgrade after data nodes
    - **Ingest Node**: Processes documents; upgrade after data nodes
    - **Machine Learning Node**: Handles ML jobs; coordinate upgrades

### **Shard Allocation**

- **Shard Allocation**: The process by which Elasticsearch automatically rebalances data across nodes to maintain even distribution.
- **Why Disable During Upgrade?**: When a node goes down for upgrade, Elasticsearch automatically moves its shards to other nodes. This causes unnecessary network I/O and delays the upgrade. Disabling allocation prevents this.
- **Rebalancing**: After a node is upgraded and rejoins, enabling allocation allows shards to rebalance. If you have 20,000+ shards in production, this can take hours.

### **Compatibility Matrix**

- A table published by Elastic that specifies which versions of Elasticsearch, Kibana, Beats, Logstash, and Agents can communicate with each other.
- **Critical Rule**: All components must fall within compatible version ranges. Mismatched versions cause communication failures and data loss.
- Example: If you upgrade Elasticsearch to 9.2.3 but your Beats agents are still on 8.18.9, the agents cannot send data.

---

## Why Upgrades Are Necessary

### **Primary Reasons**

- **Bug Fixes**: Address vulnerabilities and stability issues from previous versions
- **New Features**: Access to ML, AI capabilities, and enhanced functionality not available in older versions
- **Security Updates**: Critical for compliance and protecting against known exploits
- **End-of-Life Support**: Elastic stops supporting older versions; no assistance if issues occur on unsupported versions

### **Business Impact of Obsolete Versions**

- Organizations risk vendor abandonment; Elastic will not troubleshoot unsupported versions
- Running outdated software creates compliance and security audit failures
- Missing security patches expose infrastructure to known vulnerabilities

---

## Pre-Upgrade Checklist & Prerequisites

### **1. Verify Cluster Health**

- **Command**: `GET _cluster/health`
- **Requirement**: Cluster must be in **GREEN** state
- **Why**: A yellow or red cluster indicates ongoing issues. Upgrading with unresolved problems will compound difficulties and make rollback harder.
- **Action**: Resolve all health issues before proceeding

### **2. Verify Resource Utilization**

- **All metrics must be below 80%**:
    - CPU Utilization
    - RAM Utilization
    - Disk Space Utilization
- **Why**: Upgrades consume additional resources. If you're already at 96% RAM, the upgrade process may push you over 100%, causing nodes to fail.
- **Command**: `GET _cat/nodes?v`

### **3. Take Snapshot Backup**

- **Requirement**: Backup all cluster data before any upgrade
- **Scope**: If you have 100 TB of data, you need 100 TB of storage space for the backup
- **Storage Options**: S3, NAS, dedicated backup storage, or cloud provider storage
- **Cost Consideration**: If backup storage cost is prohibitive, document this risk explicitly in written communication to stakeholders; get sign-off acknowledging data loss risk
- **Advantage**: If upgrade fails catastrophically, you can restore from snapshot to a new cluster in minutes

### **4. Backup Configuration Files**

- **Essential Files to Back Up**:
    - `/etc/elasticsearch/elasticsearch.yml`
    - `/etc/elasticsearch/` (entire directory)
    - Keystore files (if using SSL/security credentials)
    - Any custom JVM settings
- **Why**: RPM/package upgrades may overwrite config files. Having a backup allows quick recovery.
- **Where to Store**: Same location as data snapshot, or separate backup location

### **5. Run Upgrade Assistant**

- **Location**: Kibana → Stack Management → Upgrade Assistant
- **Function**: Identifies breaking changes, deprecated features, and required fixes before upgrade
- **Action Items**:
    - **Critical Issues**: Must be resolved before upgrade. The system will not allow upgrade until critical issues are fixed.
    - **Warnings**: Can proceed with upgrade, but features may stop working (e.g., deprecations)
- **Example**: If you're using table visualizations that are no longer supported, you must recreate them as Discover searches before upgrading

### **6. Verify Compatibility Matrix**

- **Location**: Elastic official documentation (docs.elastic.co)
- **Check Points**:
    - Operating System compatibility for your version
    - Java/JVM version requirements
    - Browser support for Kibana
    - Agent/Beat version compatibility
- **Example**: If you're running CentOS 6 and want to upgrade Elasticsearch to 9.0, this is not supported. CentOS 6 only supports up to version 7.17.

### **7. Snapshot Configuration Files**

- **Before Every Upgrade**:
    - Take screenshot of cluster state
    - Document timestamps
    - Record resource utilization
- **Purpose**: Evidence that prerequisites were met. If issues arise post-upgrade, you can prove the cluster was healthy before the change.

## Upgrade Strategy & Planning

### **Environment Upgrade Sequence**

- **Recommended Order** (lowest to highest risk):
• **1. Monitoring Cluster**: If you have a dedicated monitoring cluster, upgrade here first
• **2. Dev Environment**: Upgrade next
• **3. Staging/UAT Environment**: Match production config as closely as possible
• **4. Production**: Only after successful testing in staging
    - Zero business impact if it fails or takes days to recover
    - Only impacts internal observability team
    - Developers can test with new version
    - Issues discovered early
    - Business impact is low
    - Simulates production upgrade process
    - Identifies issues in realistic environment
    - Allows team to practice and refine procedures
    - Apply lessons learned from previous environments
    - Have documented procedures ready
    - All critical issues already resolved

### **Within-Cluster Upgrade Sequence**

- **Upgrade By Node Role** (least impactful first):
• Start with **frozen/cold data nodes** (oldest, least-accessed data)
• Progress to **warm nodes**
• Then **hot nodes** (current, active data)
• Then **coordinating nodes**
• Then **ingest nodes**
• Finally **master-eligible nodes** (do NOT upgrade current master first; upgrade other master-eligible nodes first)

### **Why Upgrade Node-by-Node?**

- Maintains cluster availability; other nodes continue serving requests
- If one node fails during upgrade, the cluster still functions
- Allows time for shards to rebalance between upgrades
- Minimizes client-side errors and timeouts

---

## Important Commands / Syntax

### **Check Cluster Health**

```elixir
GET _cluster/health
```

**Expected Output**:

```elixir
{
  "status": "green",
  "number_of_nodes": 2,
  "active_shards": 20
}
```

### **Check Node Resource Utilization**

```elixir
GET _cat/nodes?v
```

**Shows**: CPU, RAM, disk usage for each node

### **Disable Shard Allocation for Cluster**

```elixir
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
```

**Effect**: Only primary shards can be allocated; replicas will not move

### **Disable Shard Allocation for Specific Node**

```elixir
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.exclude._ip": "192.168.1.51"
  }
}
```

**Effect**: Shards on node 192.168.1.51 will not move; no new shards will allocate to this node

### **Enable Shard Allocation (After Upgrade)**

```elixir
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.exclude._ip": null
  }
}
```

**Effect**: Allow normal rebalancing to resume

### **Download Elasticsearch Package (Linux)**

```elixir
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.2.3-x86_64.rpm
```

### **Backup Configuration Directory**

```elixir
sudo cp -r /etc/elasticsearch /etc/elasticsearch_backup
```

### **Stop Elasticsearch Service**

```elixir
sudo systemctl stop elasticsearch
```

### **Upgrade Elasticsearch Package (RPM)**

```elixir
sudo rpm -U elasticsearch-9.2.3-x86_64.rpm
```

**Note**: Do NOT use `-i` (install); use `-U` (upgrade) to preserve settings

### **Start Elasticsearch Service**

```elixir
sudo systemctl start elasticsearch
```

### **Check Elasticsearch Version**

```elixir
curl -X GET "localhost:9200/" | grep version
```

Or via Kibana API:

```elixir
GET /
```

### **Fix File Permissions After Upgrade**

```elixir
sudo chown -R elasticsearch:elasticsearch /etc/elasticsearch
sudo chown -R elasticsearch:elasticsearch /usr/share/elasticsearch
sudo chmod -R 755 /etc/elasticsearch
```

**Why**: RPM upgrades run as root, changing file ownership. Services run as `elasticsearch` user and need proper permissions.

### **Stop & Start Elasticsearch (Recommended vs Restart)**

```elixir
# Recommended approach:
sudo systemctl stop elasticsearch
# Wait for full shutdown
sudo systemctl start elasticsearch

# Avoid:
sudo systemctl restart elasticsearch
```

**Why**: `stop` then `start` ensures complete shutdown before restart; `restart` may not fully stop the process

---

## Step-by-Step Practical Upgrade Procedure

### **Pre-Upgrade Phase (Day Before)**

- **1. Review Upgrade Assistant** in Kibana
    - Note all critical issues and their resolutions
    - Prepare commands/configs to fix warnings
- **2. Verify Compatibility Matrix**
    - Confirm OS support
    - Confirm Java/JVM version
    - Confirm all Agent/Beat versions will be compatible
- **3. Take Full Backup**
    - Create snapshot of all indices
    - Verify backup is accessible and recoverable
    - Document backup location and timestamp
- **4. Backup Configuration**
    - Copy entire `/etc/elasticsearch` directory
    - Copy entire `/etc/kibana` directory (if upgrading Kibana)
    - Include JVM settings files
- **5. Notify Stakeholders**
    - Inform teams that upgrade is scheduled
    - Set maintenance window
    - Provide rollback plan

### **Upgrade Phase - First Node**

- **Step 1: Verify Pre-Conditions**
    - Confirm cluster is green
    - Screenshot current state
    - Document resource utilization
- **Step 2: Disable Shard Allocation**

```elixir
  PUT _cluster/settings
  {
    "persistent": {
      "cluster.routing.allocation.exclude._ip": "192.168.1.51"
    }
  }
```

- **Step 3: Stop Elasticsearch Service**

```elixir
  sudo systemctl stop elasticsearch
```

- Monitor logs for clean shutdown
- Verify service is stopped: `sudo systemctl status elasticsearch`
- **Step 4: Backup Current Version Config**

```elixir
  sudo cp -r /etc/elasticsearch /etc/elasticsearch.backup.8.19.9
```

- **Step 5: Download New Version Package**

```elixir
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.2.3-x86_64.rpm
```

- **Step 6: Upgrade Package**

```elixir
  sudo rpm -U elasticsearch-9.2.3-x86_64.rpm
```

- **Step 7: Fix File Permissions**

```elixir
  sudo chown -R elasticsearch:elasticsearch /etc/elasticsearch
  sudo chown -R elasticsearch:elasticsearch /usr/share/elasticsearch
  sudo chmod -R 755 /etc/elasticsearch
```

- **Step 8: Start Elasticsearch Service**

```elixir
  sudo systemctl start elasticsearch
```

- **Step 9: Verify Service is Running**

```elixir
  sudo systemctl status elasticsearch
  # Check logs:
  tail -f /var/log/elasticsearch/elasticsearch.log
```

- **Step 10: Verify Version Upgrade**

```elixir
  curl -X GET "localhost:9200/" | grep version
```

- **Step 11: Enable Shard Allocation**

```elixir
  PUT _cluster/settings
  {
    "persistent": {
      "cluster.routing.allocation.exclude._ip": null
    }
  }
```

- **Step 12: Wait for Cluster to Rebalance**
    - Monitor: `GET _cluster/health`
    - Wait until status returns to GREEN
    - May take several hours for large clusters

### **Upgrade Phase - Second & Subsequent Nodes**

- **Steps 1-12**: Repeat the same process for each node
- **Time Optimization**: Because you've already identified and documented issues from the first node, subsequent nodes will upgrade faster
- **Parallel Consideration**: You can upgrade multiple non-master nodes in parallel if your cluster is large, but be cautious about shard movement

## **Kibana & Other Components**

**After Elasticsearch Is Fully Upgraded**

### **Verify Kibana Can Connect**

- Check Kibana startup logs
- Confirm Elasticsearch endpoint and credentials are correct

### **Upgrade Kibana**

- Perform the Kibana upgrade
- Log into Kibana
- Check for connection errors in the logs

### **Upgrade Logstash** (if applicable)

- Follow the same upgrade process as Elasticsearch/Kibana
- Verify pipeline connectivity after upgrade

### **Upgrade Beats & Agents**

- Upgrade **Fleet Server first** (agents depend on it)
- Upgrade Elastic Agents
- Verify agents can send data to the new Elasticsearch version

---

## **Common Issues & Troubleshooting**

### **Issue 1: Permission Denied Errors After Upgrade**

**Symptom**

```
Permission denied: /etc/elasticsearch
Permission denied: /var/lib/elasticsearch
```

**Root Cause**

RPM upgrades run as `root`, which may change file ownership from the `elasticsearch` user to `root`.

**Solution**

```bash
sudochown -R elasticsearch:elasticsearch /etc/elasticsearch
sudochown -R elasticsearch:elasticsearch /var/lib/elasticsearch
sudochmod -R 755 /etc/elasticsearch
```

**Prevention**

Document these commands and execute them immediately after any RPM-based Elasticsearch upgrade.

### **Issue 2: Service Fails to Start After Upgrade**

**Symptom**: `systemctl start elasticsearch` returns quickly but service is not running

**Diagnosis**:

```elixir
sudo systemctl status elasticsearch  # Shows inactive
tail -f /var/log/elasticsearch/elasticsearch.log  # Check error messages
```

**Common Causes**:

- File permissions (see Issue 1)
- Configuration syntax errors in `elasticsearch.yml`
- JVM memory allocation too high for available RAM
- Corrupted keystore or security files

**Solution**:

- Fix permissions
- Validate `elasticsearch.yml` syntax
- Compare with backup version
- Restore config from backup if necessary

### **Issue 3: Cluster Remains Yellow/Red After Upgrade**

**Symptom**: `GET _cluster/health` returns yellow or red status

**Root Cause**: Shards failed to rebalance, or some shards remain unallocated

**Diagnosis**:

```elixir
GET _cluster/health?level=indices  # See which indices are problematic
GET _cat/shards?v  # See unallocated shards
```

**Solution**:

- Wait longer for rebalancing (can take hours for large clusters)
- Check node disk space—if >85% full, Elasticsearch won't allocate shards
- Investigate individual index allocation issues
- If critical, consult Elastic documentation for your specific error

### **Issue 4: Data/Dashboards Missing After Upgrade**

**Symptom**: Indices, dashboards, or visualizations disappeared after upgrade

**Root Cause**: Breaking changes in Kibana (e.g., table visualization no longer supported); indices deleted during backup/restore process

**Prevention**:

- Run Upgrade Assistant before upgrade; identify deprecated features
- Take screenshot of all dashboards before upgrade
- Recreate deprecated visualizations using new methods

**Recovery**:

- Restore from snapshot if available
- Manually recreate dashboards/visualizations

### **Issue 5: Agents Cannot Send Data After Elasticsearch Upgrade**

**Symptom**: Kibana shows no new data arriving after Elasticsearch upgrade

**Root Cause**: Agent version incompatible with new Elasticsearch version

**Diagnosis**:

```elixir
# Check Compatibility Matrix for your versions
# Verify agent version:
filebeat --version
metricbeat --version
```

**Solution**:

- Upgrade all Beats/Agents to compatible version
- Refer to Compatibility Matrix for required version ranges
- Example: If Elasticsearch is now 9.2.3, Beats must be between 8.19.x and 9.2.x

### **Issue 6: Upgrade Takes Unexpectedly Long**

**Symptom**: Shard rebalancing or data migration during upgrade is very slow

**Root Cause**: Cluster is performing heavy rebalancing; disk I/O is bottlenecked; or network is slow

**Mitigation**:

- Check cluster health: `GET _cluster/health`
- Check shard allocation: `GET _cat/shards?v`
- Monitor system resources: `GET _cat/nodes?v`
- Be patient; large clusters with many shards can take 4-8 hours to rebalance

**Prevention**:

- Disable shard allocation during node upgrade (as documented in procedures)
- Reduce cluster load before upgrade

### **Issue 7: Master Node Election Problems**

**Symptom**: Cluster becomes unavailable; shows "no master elected"

**Root Cause**: You upgraded the current master node without ensuring other master-eligible nodes were ready

**Prevention**:

- Always upgrade non-master master-eligible nodes first
- Upgrade the current master last
- Monitor master eligibility: `GET _cat/nodes?v | grep master`

**Recovery**:

- Ensure at least one master-eligible node is running and healthy
- Cluster will auto-elect new master
- Wait for cluster to stabilize (green status)

---


## Summary / Key Takeaways

### **Core Principles**

- **Never upgrade to the latest version immediately**—always wait for N-1 or N-2 stability
- **Cluster health must be GREEN** before any upgrade; do not proceed with yellow or red status
- **Node-by-node upgrades** prevent downtime; at least one node is always running
- **Upgrade from least impactful to most impactful**: frozen data tiers → hot tiers → coordinating nodes → master nodes
- **Always disable shard allocation** for the node being upgraded to prevent unnecessary data movement
- **Compatibility Matrix is critical**—all components must fall within compatible version ranges or data loss occurs

### **Pre-Upgrade Checklist (Must Complete)**

- Cluster is GREEN with no issues
- Resource utilization below 80% (CPU, RAM, Disk)
- Full data snapshot created and verified
- Configuration files backed up
- Upgrade Assistant run; critical issues resolved
- Compatibility Matrix checked for OS, Java, agents
- Screenshots and documentation prepared

### **During Upgrade**

- Disable shard allocation for the node
- Back up configuration directory
- Stop service cleanly
- Download and upgrade package
- Fix file permissions (chown/chmod)
- Start service and verify version
- Re-enable shard allocation
- Wait for cluster to rebalance and return to GREEN

### **Post-Upgrade Verification**

- Verify cluster is GREEN
- Verify all dashboards exist and display correctly
- Verify all users/roles are preserved
- Verify ILM policies are intact
- Verify agents can send data
- Verify alerts/watchers are enabled
- Take final screenshot of successful state

### **Common Mistakes to Avoid**

- Upgrading directly to the latest version without waiting for stability
- Not taking a data snapshot before upgrade
- Not verifying Compatibility Matrix before upgrading agents
- Forgetting to disable shard allocation, causing unnecessary rebalancing
- Upgrading the current master node first instead of last
- Using `systemctl restart` instead of `stop` then `start`
- Not documenting and screenshotting the upgrade process
- Upgrading in production without testing in staging first


