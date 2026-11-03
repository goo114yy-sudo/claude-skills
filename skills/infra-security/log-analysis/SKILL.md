---
name: log-analysis
description: Cross-platform log analysis patterns and techniques
homepage: https://www.gnu.org/software/grep/manual/

metadata:
  emoji: "📋"
  version: "1.0.0"
  author: "Gourav Shah"
  license: "MIT"

  requires:
    any_bins: ["grep", "awk", "jq"]

  tags:
    - logs
    - debugging
    - analysis
    - sre
---

# Log Analysis

Techniques for analyzing logs across different platforms and formats.

## When to Use This Skill

Use this skill when:
- Investigating errors or issues
- Searching for patterns in logs
- Correlating events across systems
- Building log queries

## Universal Patterns

### Search Basics

```bash
# Simple search
grep "ERROR" app.log

# Case insensitive
grep -i "error" app.log

# With line numbers
grep -n "ERROR" app.log

# With context (3 lines before/after)
grep -C 3 "ERROR" app.log
grep -B 3 -A 3 "ERROR" app.log

# Count occurrences
grep -c "ERROR" app.log
```

### Multiple Patterns

```bash
# OR - match any
grep -E "ERROR|WARN|FATAL" app.log

# AND - match all (same line)
grep "ERROR" app.log | grep "database"

# NOT - exclude pattern
grep "ERROR" app.log | grep -v "expected"
```

### Time-Based Filtering

```bash
# Last hour (if timestamp is in log)
grep "$(date '+%Y-%m-%d %H')" app.log

# Date range
awk '/2024-01-15 10:00/,/2024-01-15 11:00/' app.log

# Recent entries (tail)
tail -1000 app.log | grep "ERROR"
```

## Kubernetes Logs

### Pod Logs

```bash
# Current logs
kubectl logs <pod>

# Previous container (after crash)
kubectl logs <pod> --previous

# Follow live
kubectl logs -f <pod>

# Last N lines
kubectl logs --tail=100 <pod>

# Since time
kubectl logs --since=1h <pod>

# All containers in pod
kubectl logs <pod> --all-containers

# By label
kubectl logs -l app=nginx --all-containers
```

### Multi-Pod Logs

```bash
# All pods with label
kubectl logs -l app=myapp --all-containers --prefix

# Stern (better multi-pod tailing)
stern myapp -n namespace

# With regex
stern "myapp-.*" --since 1h
```

### Search in Logs

```bash
# Grep in kubectl logs
kubectl logs <pod> | grep -i error

# With timestamps
kubectl logs --timestamps <pod> | grep "ERROR"

# Recent errors
kubectl logs --since=1h <pod> | grep -E "ERROR|Exception"
```

## Docker Logs

```bash
# Basic logs
docker logs <container>

# Follow
docker logs -f <container>

# Tail
docker logs --tail 100 <container>

# Since time
docker logs --since 1h <container>

# With timestamps
docker logs -t <container>

# Search
docker logs <container> 2>&1 | grep "ERROR"
```

## JSON Logs (jq)

### Basic Parsing

```bash
# Pretty print
cat log.json | jq .

# Extract field
cat log.json | jq '.message'

# Multiple fields
cat log.json | jq '{time: .timestamp, msg: .message}'
```

### Filtering

```bash
# Filter by field value
cat log.json | jq 'select(.level == "error")'

# Contains string
cat log.json | jq 'select(.message | contains("database"))'

# Multiple conditions
cat log.json | jq 'select(.level == "error" and .service == "api")'
```

### JSONL (JSON Lines)

```bash
# Each line is JSON
cat logs.jsonl | jq -c 'select(.level == "error")'

# Extract field from each line
cat logs.jsonl | jq -r '.message'

# Count by level
cat logs.jsonl | jq -r '.level' | sort | uniq -c | sort -rn
```

## CloudWatch Logs

```bash
# Tail logs
aws logs tail /aws/lambda/function-name --follow

# Since time
aws logs tail /aws/lambda/function-name --since 1h

# Filter pattern
aws logs tail /aws/lambda/function-name --filter-pattern "ERROR"
```

### CloudWatch Insights

```bash
# Start query
aws logs start-query \
  --log-group-name /aws/lambda/function-name \
  --start-time $(date -d '1 hour ago' +%s) \
  --end-time $(date +%s) \
  --query-string '
    fields @timestamp, @message
    | filter @message like /ERROR/
    | sort @timestamp desc
    | limit 50
  '

# Get results
aws logs get-query-results --query-id <query-id>
```

## Common Patterns

### Error Aggregation

```bash
# Top error messages
grep "ERROR" app.log | sort | uniq -c | sort -rn | head -20

# Errors per hour
grep "ERROR" app.log | awk '{print $1, $2}' | cut -d: -f1 | uniq -c
```

### Response Time Analysis

```bash
# Extract response times (assuming format: "response_time=123ms")
grep -oP 'response_time=\K\d+' app.log | \
  awk '{sum+=$1; count++} END {print "avg:", sum/count, "count:", count}'

# Slow requests (>1000ms)
grep -P 'response_time=\d{4,}' app.log
```

### Status Code Analysis

```bash
# Count by status code
grep -oP 'status=\K\d+' app.log | sort | uniq -c | sort -rn

# 5xx errors
grep -P 'status=5\d\d' app.log
```

### IP/User Analysis

```bash
# Top IPs
grep -oP '\d+\.\d+\.\d+\.\d+' access.log | sort | uniq -c | sort -rn | head -10

# Requests per user
grep -oP 'user=\K\S+' app.log | sort | uniq -c | sort -rn
```

## Correlation Techniques

### By Request ID

```bash
# Find all logs for a request
grep "request_id=abc123" *.log

# Across pods
kubectl logs -l app=myapp --all-containers | grep "request_id=abc123"
```

### By Timestamp

```bash
# Events around a specific time
awk '/2024-01-15 10:30:4/,/2024-01-15 10:30:5/' app.log
```

### Across Services

```bash
# Find related events
for service in api worker database; do
  echo "=== $service ==="
  grep "order_id=12345" /var/log/$service.log
done
```

## Log Formats

### Apache/Nginx Access Logs

```bash
# Status codes
awk '{print $9}' access.log | sort | uniq -c | sort -rn

# Response times (if configured)
awk '{print $NF}' access.log | sort -n | tail -20

# Top URLs
awk '{print $7}' access.log | sort | uniq -c | sort -rn | head -10
```

### Syslog

```bash
# By service
grep "sshd" /var/log/syslog

# Failed logins
grep "Failed password" /var/log/auth.log

# By severity
grep -E "(error|crit|alert|emerg)" /var/log/syslog
```

## Quick Reference

```bash
# Find errors in last hour
grep "$(date '+%Y-%m-%d %H')" app.log | grep -i error

# Top 10 error messages
grep -i error app.log | sort | uniq -c | sort -rn | head -10

# JSON logs: filter and format
cat logs.jsonl | jq 'select(.level=="error") | "\(.timestamp) \(.message)"'

# Kubernetes: errors across all pods
kubectl logs -l app=myapp --all-containers --since=1h | grep -i error

# AWS CloudWatch: recent errors
aws logs tail /aws/lambda/func --since 1h --filter-pattern "ERROR"
```

## Related Skills

- **k8s-debug**: For Kubernetes-specific log analysis
- **docker-ops**: For Docker log management
- **incident-response**: For correlating logs during incidents
