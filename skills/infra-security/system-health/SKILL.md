---
name: system-health
description: Quick system health checks for disk, memory, CPU, and processes
homepage: https://man7.org/linux/man-pages/

metadata:
  emoji: "💊"
  version: "1.0.0"
  author: "Gourav Shah"
  license: "MIT"

  requires:
    any_bins: ["top", "htop", "ps"]

  os: ["darwin", "linux"]

  tags:
    - system
    - monitoring
    - health
    - sre
---

# System Health

Quick commands for checking system health on Linux and macOS.

## When to Use This Skill

Use this skill when:
- System feels slow or unresponsive
- Need to check resource usage
- Investigating high load alerts
- Before/after deployments
- Routine health checks

## Quick Health Overview

### One-Liner Health Check

```bash
# Linux - quick overview
echo "=== Load ===" && uptime && echo "\n=== Memory ===" && free -h && echo "\n=== Disk ===" && df -h / && echo "\n=== Top Processes ===" && ps aux --sort=-%cpu | head -6

# macOS - quick overview
echo "=== Load ===" && uptime && echo "\n=== Memory ===" && vm_stat | head -5 && echo "\n=== Disk ===" && df -h / && echo "\n=== Top Processes ===" && ps aux -r | head -6
```

## CPU & Load

### Check System Load

```bash
# Current load average (1, 5, 15 min)
uptime

# Continuous load monitoring
w
```

**Interpreting Load Average:**
- Load < CPU cores = healthy
- Load = CPU cores = fully utilized
- Load > CPU cores = overloaded

```bash
# Check number of CPU cores
# Linux
nproc

# macOS
sysctl -n hw.ncpu
```

### CPU Usage by Process

```bash
# Top CPU consumers
ps aux --sort=-%cpu | head -10

# macOS
ps aux -r | head -10

# Real-time with top
top -o cpu

# Better: htop (if installed)
htop
```

## Memory

### Check Memory Usage

```bash
# Linux - human readable
free -h

# Linux - detailed
cat /proc/meminfo | head -10

# macOS
vm_stat

# macOS - more readable
memory_pressure
```

### Memory by Process

```bash
# Top memory consumers
ps aux --sort=-%mem | head -10

# macOS
ps aux -m | head -10
```

### Swap Usage

```bash
# Linux
swapon --show
free -h | grep Swap

# macOS
sysctl vm.swapusage
```

## Disk

### Disk Space

```bash
# All mounted filesystems
df -h

# Specific path
df -h /

# Inodes (Linux)
df -i
```

### Disk Usage by Directory

```bash
# Current directory breakdown
du -sh */ | sort -hr

# Top 10 largest directories
du -h / 2>/dev/null | sort -hr | head -10

# Specific directory
du -sh /var/log/*
```

### Find Large Files

```bash
# Files over 100MB
find / -type f -size +100M 2>/dev/null | head -20

# With sizes
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null | head -20
```

## Processes

### List Processes

```bash
# All processes
ps aux

# Tree view (Linux)
ps auxf

# Tree view (macOS)
pstree

# By user
ps -u <username>
```

### Find Specific Process

```bash
# By name
ps aux | grep <process-name>

# Better: pgrep
pgrep -la <process-name>

# With full command
ps aux | grep -E "PID|<process-name>" | grep -v grep
```

### Process Details

```bash
# By PID
ps -p <pid> -o pid,ppid,user,%cpu,%mem,stat,start,time,command

# Open files by process
lsof -p <pid>

# Network connections by process
lsof -i -P -n | grep <pid>
```

### Kill Processes

```bash
# Graceful kill
kill <pid>

# Force kill
kill -9 <pid>

# Kill by name
pkill <process-name>

# Kill all matching
killall <process-name>
```

## Network

### Quick Network Check

```bash
# Listening ports
# Linux
ss -tlnp

# macOS
lsof -i -P -n | grep LISTEN

# Or netstat
netstat -tlnp   # Linux
netstat -an | grep LISTEN  # macOS
```

### Active Connections

```bash
# All connections
ss -tanp  # Linux
netstat -an  # macOS

# Connection counts by state
ss -tan | awk '{print $1}' | sort | uniq -c | sort -rn
```

## System Info

### OS Information

```bash
# Linux
cat /etc/os-release
uname -a

# macOS
sw_vers
uname -a
```

### Uptime & Boot Time

```bash
uptime

# Boot time
who -b
```

### Hardware Info

```bash
# CPU info (Linux)
lscpu

# Memory info (Linux)
cat /proc/meminfo

# macOS
system_profiler SPHardwareDataType
```

## Quick Troubleshooting

| Symptom | Check | Command |
|---------|-------|---------|
| System slow | Load average | `uptime` |
| High CPU | Top processes | `ps aux --sort=-%cpu \| head` |
| Out of memory | Memory usage | `free -h` |
| Disk full | Disk space | `df -h` |
| Process hung | Process state | `ps aux \| grep <name>` |
| Port in use | Listening ports | `ss -tlnp` or `lsof -i :<port>` |

## Health Check Script

```bash
#!/bin/bash
echo "========== SYSTEM HEALTH =========="
echo ""
echo "--- Hostname & Uptime ---"
hostname && uptime
echo ""
echo "--- Load Average ---"
cat /proc/loadavg 2>/dev/null || uptime | awk -F'load average:' '{print $2}'
echo ""
echo "--- Memory ---"
free -h 2>/dev/null || vm_stat | head -5
echo ""
echo "--- Disk ---"
df -h / /var /tmp 2>/dev/null
echo ""
echo "--- Top 5 CPU ---"
ps aux --sort=-%cpu 2>/dev/null | head -6 || ps aux -r | head -6
echo ""
echo "--- Top 5 Memory ---"
ps aux --sort=-%mem 2>/dev/null | head -6 || ps aux -m | head -6
echo "===================================="
```

## Related Skills

- **docker-ops**: For container resource monitoring
- **k8s-debug**: For Kubernetes node/pod resources
- **incident-response**: For systematic troubleshooting
