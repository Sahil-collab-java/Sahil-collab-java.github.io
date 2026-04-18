---
title: Elasticsearch Alerting & Watchers
date: 2026-04-18
categories: [Elastic Search (ELK) , Elastic Search , Kibana]
tags: [elastic,elk,elastic search,kibana,elastic upgrade,alerting,watchers]
---

## 1. Overview / Introduction

**Alerting in Elasticsearch** enables automated monitoring and notifications based on specific conditions in your data. Instead of manually monitoring dashboards 24/7, you can configure alerts that automatically notify relevant teams when critical events occur (server downtime, high CPU utilization, errors, etc.).

**Key Benefits:**

- Eliminates need for constant manual monitoring
- Provides immediate notifications via email, Microsoft Teams, Slack, etc.
- Enables proactive response to issues before business impact
- Supports team-specific alerts based on application ownership

**Two Main Approaches:**

1. **Rules** - Simpler, UI-based approach for straightforward alerting scenarios
2. **Watchers** - Advanced, JSON-based approach for complex alerting logic (X-Pack feature)

---

## 2. Important Concepts & Definitions

### Alerting Components

**Connectors:**

- Configuration that stores credentials and connection details for alert destinations
- Must be configured before creating alerts
- Examples: Gmail connector, Microsoft Teams connector, Slack connector

**Trigger/Schedule:**

- Defines how frequently the alert condition is checked
- Examples: every 5 minutes, every hour, daily

**Input:**

- Specifies which index to query
- Defines which fields to retrieve

**Query/Filter:**

- Conditions that must be met for alert to trigger
- Examples: `method_type = DELETE`, `browser_name = Chrome`

**Range:**

- Time window for data analysis
- Examples: last 15 minutes, last 30 days, now minus 1 day

**Condition:**

- Threshold that determines when to send alert
- Examples: `hits >= 1`, `count > 10`

**Action:**

- What happens when condition is met
- Examples: send email, create index, ping Microsoft Teams

### Priority Levels (P1, P2, P3, P4)

**P1 (Priority 1):**

- Critical issues with immediate business impact
- Example: Complete server downtime
- Response time: ~15 minutes acknowledgment required
- 24/7 monitoring team responds immediately

**P2 (Priority 2):**

- High priority but not critical
- Example: High CPU utilization (>85%)
- Response time: ~30-45 minutes acknowledgment

**P3 (Priority 3):**

- Medium priority issues
- Example: Website slowness

**P4 (Priority 4):**

- Low priority issues

### X-Pack Features

X-Pack is Elasticsearch's premium feature bundle that includes:

- **Machine Learning**
- **Alerting (Watchers)**
- **Graph Capabilities**
- **JDBC & ODBC Connectivity**
- **Advanced Security** (authentication via SSO, LDAP, etc.)
- **Auditing** (tracks who logged in, modified dashboards, deleted indices)
- **Document-level Security** (field-level access control)

**Trial:** 30-day free trial available via Stack Management

---

## 3. Important Commands / Syntax

### Telnet Command (Check Server Connectivity)

```elixir
# Check if Gmail SMTP server is accessible
telnet smtp.gmail.com 465

# Expected output: Connection successful
# If fails: Connection refused or timeout
```

### Store Email Password in Elasticsearch Keystore

```elixir
# Add secure password to keystore
bin/elasticsearch-keystore add xpack.notification.email.account.gmail.smtp.secure_password

# List keystore entries
bin/elasticsearch-keystore list
```

### Elasticsearch Configuration (elasticsearch.yml)

```elixir
xpack.notification.email.account:
  gmail_account:
    profile: gmail
    smtp:
      auth: true
      host: smtp.gmail.com
      port: 465
      user: your-email@gmail.com
```

**Note:** Password is stored in keystore, not in config file. Restart Elasticsearch after configuration changes.

---

## 4. Step-by-Step Practical Procedure

### A. Configure Email Connector

### Step 1: Enable Trial License

1. Go to **Stack Management** → **License Management**
2. Click **Start Trial** (30-day trial)
3. Trial expires in 30 days from activation

### Step 2: Create Gmail App Password

1. Go to Google Account settings
2. Navigate to **Security** → **2-Step Verification** → **App Passwords**
3. Create new app password named "Elasticsearch"
4. Copy the 16-character password (save it securely)

### Step 3: Test Server Connectivity

```elixir
telnet smtp.gmail.com 465
# Should return: "Connecting to smtp.gmail.com..."
```

### Step 4: Configure Elasticsearch

```elixir
# Add password to keystore
bin/elasticsearch-keystore add xpack.notification.email.account.gmail.smtp.secure_password
# Enter app password when prompted
```

Add to `elasticsearch.yml`:

```elixir
xpack.notification.email.account:
  gmail_account:
    profile: gmail
    smtp:
      auth: true
      host: smtp.gmail.com
      port: 465
      user: your-email@gmail.com
```

Restart Elasticsearch service.

### Step 5: Create Connector in Kibana

1. Go to **Stack Management** → **Connectors**
2. Click **Create Connector** → **Email**
3. Fill in details:
    - **Name:** Gmail_Connector
    - **Sender:** [your-email@gmail.com](mailto:your-email@gmail.com)
    - **Service:** Gmail
    - **Username:** [your-email@gmail.com](mailto:your-email@gmail.com)
    - **Password:** [App password from Step 2]
4. Click **Save & Test**
5. Send test email to verify

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image.png)

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image1.png)

---

### B. Create Alert Using Discover (Query Preparation)

### Step 1: Identify Data in Discover

1. Go to **Discover**
2. Select index pattern (e.g., `data-*`)
3. Apply filters for your alert condition:
    - Example: `method_type: DELETE` AND `browser_name: Chrome`

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image2.png)

### Step 2: Verify Results

- Check that data displays correctly
- Note the field names (exact case matters)
- Verify time range (last 15 minutes, last 30 days, etc.)

### Step 3: Extract Query

1. Click **Inspect** on the query
2. Click **Request** → **Copy to clipboard**
3. Open **Dev Tools**
4. Paste the copied query
5. Locate the **second filter** section (before `range`)
6. Copy filter content between opening bracket and `range`

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image3.png)

---

### C. Create Watcher

### Step 1: Navigate to Watchers

1. Go to **Stack Management** → **Watcher**
2. Click **Create** → **Create Advanced Watch**

### Step 2: Define Watcher Structure

```elixir
{
  "trigger": {
    "schedule": {
      "interval": "5m"
    }
  },
  "input": {
    "search": {
      "request": {
        "search_type": "query_then_fetch",
        "indices": [
          "logstash-kv"
        ],
        "rest_total_hits_as_int": true,
        "body": {
          "size": 10,
          "_source": [
            "browser",
            "url",
            "method",
            "@timestamp"
          ],
          "query": {
            "bool": {
              "filter": [
                {
                  "term": {
                    "method.keyword": "POST"
                  }
                },
                {
                  "term": {
                    "browser.keyword": "Chrome"
                  }
                },
                {
                  "range": {
                    "@timestamp": {
                      "gte": "now-30d/d",
                      "lte": "now"
                    }
                  }
                }
              ]
            }
          }
        }
      }
    }
  },
  "condition": {
    "compare": {
      "ctx.payload.hits.total": {
        "gte": 1
      }
    }
  },
  "actions": {
    "send_email": {
      "email": {
        "profile": "gmail",
        "from": "sahilrangari07@gmail.com",
        "to": [
          "sahilrangari07@gmail.com",
          "aishwarya1424petkar@gmail.com"
        ],
        "subject": "Data Deleted from Amazon Application",
        "body": {
          "html": """
          <h3>Data Deleted from Amazon Application</h3>
          <p>Total records found (Last 30 Days): {{ctx.payload.hits.total}}</p>
          <table border='1'>
            <tr>
              <th>Browser</th>
              <th>Method</th>
              <th>URL</th>
              <th>Timestamp</th>
            </tr>
            {{#ctx.payload.hits.hits}}
            <tr>
              <td>{{_source.browser}}</td>
              <td>{{_source.method}}</td>
              <td>{{_source.url}}</td>
              <td>{{_source.@timestamp}}</td>
            </tr>
            {{/ctx.payload.hits.hits}}
          </table>
          """
        }
      }
    }
  }
}
```

### Step 3: Test Watcher

1. Click **Simulate** to test without sending actual email
2. Check results:
    - **Condition Met:** Shows if alert would trigger
    - **Actions:** Shows email content that would be sent
3. If satisfied, click **Create Watch**

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image4.png)

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image5.png)

### Step 4: Force Execute (Manual Test)

1. Click on watcher ID
2. Click **Execute** (forces immediate execution)
3. Check email inbox for alert

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image6.png)

### Step 5: Manage Watcher

- **Deactivate:** Temporarily disable without deleting
- **Delete:** Permanently remove watcher
- **View History:** See execution history (success/failure)

![image.png](assets/images/2026-04-18-ElasticSearch-Alerting-Watchers/image7.png)

---

### D. Create Alert Using Rules (Simpler Alternative)

1. Go to **Stack Management** → **Rules**
2. Click **Create Rule**
3. Select rule type: **Elasticsearch Query**
4. Configure:
    - **Index:** data-*
    - **Time field:** timestamp
    - **Query:** Use KQL (e.g., `method_type:DELETE`)
    - **Threshold:** Matches >= 1
    - **Check every:** 5 minutes
    - **For the last:** 15 minutes
5. **Action:** Email
6. Select connector and configure email template
7. Save and activate

---

## 5. Common Issues & Troubleshooting

### Issue 1: Gmail Connection Refused

**Error:** `Could not open connection to the host on port 465`

**Solutions:**

- Verify Gmail SMTP settings: `smtp.gmail.com` on port `465`
- Check firewall allows outbound connections on port 465
- Test with telnet: `telnet smtp.gmail.com 465`
- Ensure Elasticsearch server has internet access

### Issue 2: Authentication Failed

**Error:** `Application-specific password required`

**Solutions:**

- Do NOT use regular Gmail password
- Create App Password in Google Account Security settings
- Use 16-character app password in keystore
- Enable 2-Step Verification in Google Account first

### Issue 3: Watcher Not Triggering

**Possible Causes:**

1. **Condition not met:** Check actual hit count in Discover
2. **Wrong time range:** Verify `range` matches data timestamps
3. **Filter errors:** Ensure field names match exactly (case-sensitive)
4. **Watcher deactivated:** Check watcher status

**Debug Steps:**

1. Test query in Discover first
2. Use **Simulate** to see what watcher would return
3. Check watcher execution history
4. Verify condition logic (gte, lte, gt, lt)

### Issue 4: No Email Received

**Solutions:**

- Check spam/junk folder
- Verify email addresses in `to` field
- Check connector test succeeded
- Review watcher execution history for errors
- Ensure Elasticsearch service restarted after keystore changes

### Issue 5: Too Many Alerts

**Problem:** Alert triggers every minute for old data

**Solution:**

- Adjust time range: Change from `now-30d` to `now-15m`
- Increase schedule interval: Change from `1m` to `15m`
- Add more specific filters to reduce false positives

---


### 7. Summary / Key Takeaways

Core Concepts
- **Alerting eliminates 24/7 manual monitoring** by automatically notifying teams of critical events
- **Two approaches:** Rules (simple, UI-based) and Watchers (advanced, JSON-based)
- **X-Pack features** (including Watchers) require paid license but offer 30-day trial

Email Configuration Essentials
- Use **App Password**, not regular Gmail password
- Store password in **Elasticsearch keystore** for security
- Always **restart Elasticsearch** after keystore/config changes
- Test connectivity with `telnet smtp.gmail.com 465`

 Watcher Structure
```
Trigger (when to check) 
  → Input (which index + filters) 
    → Condition (threshold to meet) 
      → Action (what to do)

### Best Practices

1. **Test queries in Discover first** before creating watchers
2. **Use appropriate time ranges** (last 15m for real-time, not 30d)
3. **Limit result size** to avoid overwhelming email bodies
4. **Use priority levels** (P1, P2, P3) to indicate urgency
5. **Simulate watchers** before activation to verify behavior
6. **Deactivate instead of delete** when temporarily disabling alerts

### Common Pitfalls to Avoid

- Using regular Gmail password instead of App Password
- Forgetting to restart Elasticsearch after config changes
- Setting time range too wide (causes duplicate alerts)
- Not testing queries in Discover before implementing in watcher
- Case-sensitive field names in filters

### Production Considerations

- **Service Now integration** is standard in most organizations for ticketing
- **Acknowledge P1 alerts within 15 minutes** (organizational standard)
- **Maintain alert history** for compliance and troubleshooting
- **Monitor watcher execution logs** to identify failing alerts