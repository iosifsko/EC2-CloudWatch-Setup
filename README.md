# BistraWP EC2 Metrics to CloudWatch

This README documents how we configured **CloudWatch Agent** on an Ubuntu EC2 instance running Apache to collect system-level metrics and send them to Amazon CloudWatch. This setup powers a live metrics dashboard and prepares the system for Grafana integration.

---

## 📅 Date Completed
2025-05-27 (UTC)

---

## ✅ What We Set Up

### 🔧 EC2 CloudWatch Agent Metrics Pipeline
- **Platform**: Ubuntu (EC2)
- **Metrics collected**:
  - CPU: `cpu_usage_idle`, `cpu_usage_user`, `cpu_usage_system`, `cpu_usage_iowait`
  - Memory: `mem_used_percent`
  - Disk: `disk_used_percent`, `inodes_free`
  - Swap: `swap_used_percent`
  - Network: `tcp_established`, `tcp_time_wait`
  - DiskIO: `read_bytes`, `write_bytes`, `reads`, `writes`

---

## 🔐 IAM Role
- Attached policy: `CloudWatchAgentServerPolicy`
- Attached read access: `CloudWatchReadOnlyAccess`

---

## 🧰 Installation Steps

### 1. Install the CloudWatch Agent
```bash
sudo apt update
sudo apt install amazon-cloudwatch-agent -y
```

### 2. Run the CloudWatch Config Wizard
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```
Followed prompts to:
- Run as `root`
- Collect advanced metrics
- Monitor CPU, memory, disk, swap, and network stats
- Save config locally (not SSM)
- Skip StatsD, CollectD, and logs for now

### 3. Start the Agent
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
  -s
```

---

## 🔍 Verification

### Agent Status
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

### Check Metrics via CLI
```bash
aws cloudwatch list-metrics --namespace CWAgent --region us-west-2
```

> Required: Attach `CloudWatchReadOnlyAccess` to the EC2 instance role

---

## 📊 CloudWatch Console

- Navigate to **CloudWatch > Metrics > CWAgent**
- Confirm live data for:
  - `mem_used_percent`
  - `cpu_usage_user`
  - `disk_used_percent` (on `/`)
- Optional: Add to a **CloudWatch Dashboard**

---

## 🧭 Next Step

→ Connect CloudWatch to Grafana Labs for real-time metric visualization and alerting.
