---
name: incident-response
description: Structured incident response and diagnosis workflows
homepage: https://sre.google/sre-book/managing-incidents/

metadata:
  emoji: "🚨"
  version: "1.0.0"
  author: "Gourav Shah"
  license: "MIT"

  requires:
    any_bins: ["kubectl", "docker", "aws"]

  tags:
    - incidents
    - sre
    - troubleshooting
    - on-call
---

# Incident Response

Structured workflow for diagnosing and resolving production incidents.

## When to Use This Skill

Use this skill when:
- An alert has fired
- Users report issues
- Monitoring shows anomalies
- System behavior is unexpected
- You're the on-call responder

## Incident Response Phases

### Phase 1: Triage (First 5 minutes)

**Goal:** Assess severity and impact.

#### Quick Assessment Questions
1. What is broken? (service, feature, infrastructure)
2. Who is affected? (all users, subset, internal only)
3. When did it start? (correlate with deployments/changes)
4. Is it getting worse? (check error rate trend)

#### Severity Classification

| Severity | Impact | Response |
|----------|--------|----------|
| **SEV1** | Complete outage, all users affected | All hands, exec notification |
| **SEV2** | Major feature broken, many users affected | Primary + backup on-call |
| **SEV3** | Minor feature broken, some users affected | Primary on-call |
| **SEV4** | No user impact, potential issue | Next business day |

### Phase 2: Context Gathering (5-15 minutes)

**Goal:** Collect data to understand the problem.

#### Kubernetes Context

```bash
# Cluster health overview
kubectl get nodes
kubectl get pods -A | grep -v Running | grep -v Completed

# Recent events
kubectl get events -A --sort-by='.lastTimestamp' | tail -30

# Check specific service
kubectl get pods -l app=<service-name> -o wide
kubectl logs -l app=<service-name> --tail=100
```

#### Recent Changes

```bash
# Recent deployments
kubectl get deployments -A -o custom-columns=\
'NAMESPACE:.metadata.namespace,NAME:.metadata.name,UPDATED:.metadata.creationTimestamp' \
| sort -k3 -r | head -10

# Git history (if in repo)
git log --oneline --since="2 hours ago"
```

#### Metrics Check

```bash
# If Prometheus available
# Check error rates, latency, traffic

# If CloudWatch
aws cloudwatch get-metric-statistics \
  --namespace <namespace> \
  --metric-name <metric> \
  --start-time $(date -u -v-1H +%Y-%m-%dT%H:%M:%SZ) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) \
  --period 60 \
  --statistics Average
```

### Phase 3: Diagnosis (15-30 minutes)

**Goal:** Identify root cause.

#### Common Root Causes

1. **Recent deployment** - Check if timing correlates
2. **Resource exhaustion** - CPU, memory, disk, connections
3. **External dependency failure** - Database, API, DNS
4. **Configuration change** - ConfigMaps, secrets, feature flags
5. **Traffic spike** - Unexpected load
6. **Certificate/credential expiry** - Auth failures
7. **Infrastructure issue** - Node failure, network partition

#### Diagnosis Decision Tree

```
Is there a recent deployment?
├── Yes → Check deployment logs, consider rollback
└── No → Continue

Are pods crashing?
├── Yes → Check logs: kubectl logs <pod> --previous
└── No → Continue

Are resources exhausted?
├── Yes → Scale up or optimize
└── No → Continue

Is an external dependency failing?
├── Yes → Check dependency status, implement fallback
└── No → Continue

Is there a traffic spike?
├── Yes → Scale up, enable rate limiting
└── No → Escalate for deeper investigation
```

### Phase 4: Mitigation (ASAP)

**Goal:** Restore service, even if root cause unknown.

#### Quick Mitigations

**Rollback Deployment:**
```bash
# Kubernetes rollback
kubectl rollout undo deployment/<name> -n <namespace>
kubectl rollout status deployment/<name> -n <namespace>
```

**Scale Up:**
```bash
# Increase replicas
kubectl scale deployment/<name> --replicas=5 -n <namespace>
```

**Restart Pods:**
```bash
# Rolling restart
kubectl rollout restart deployment/<name> -n <namespace>
```

**Toggle Feature Flag:**
```bash
# If feature flags available, disable problematic feature
```

**Redirect Traffic:**
```bash
# If multiple regions, redirect away from affected region
```

### Phase 5: Communication

**Goal:** Keep stakeholders informed.

#### Status Update Template

```
**Incident Update - [SERVICE] - [SEV LEVEL]**

**Status:** Investigating / Identified / Mitigating / Resolved
**Impact:** [Who/what is affected]
**Start Time:** [When it started]
**Current Actions:** [What we're doing]
**Next Update:** [When to expect next update]

---
Incident Commander: [Name]
```

#### Communication Cadence

| Severity | Update Frequency |
|----------|------------------|
| SEV1 | Every 15 minutes |
| SEV2 | Every 30 minutes |
| SEV3 | Every hour |
| SEV4 | End of day |

### Phase 6: Resolution

**Goal:** Confirm service is restored.

#### Verification Checklist

- [ ] Error rates returned to baseline
- [ ] Latency returned to baseline
- [ ] All pods healthy
- [ ] Synthetic monitors passing
- [ ] User reports have stopped

#### Close Incident

```
**Incident Resolved - [SERVICE]**

**Duration:** [Start] to [End] ([X] minutes)
**Root Cause:** [Brief description]
**Resolution:** [What fixed it]
**Follow-ups:** [Links to action items]

Post-mortem scheduled: [Date/Time]
```

## Post-Incident

### Post-Mortem Template

```markdown
# Incident Post-Mortem: [Title]

**Date:** [Date]
**Duration:** [Duration]
**Severity:** [SEV Level]
**Authors:** [Names]

## Summary
[2-3 sentence summary]

## Impact
- Users affected: [Number/percentage]
- Revenue impact: [If applicable]
- SLA impact: [If applicable]

## Timeline
- HH:MM - [Event]
- HH:MM - [Event]
- HH:MM - [Event]

## Root Cause
[Detailed explanation]

## Resolution
[How it was fixed]

## Lessons Learned
### What went well
- [Item]

### What could be improved
- [Item]

## Action Items
- [ ] [Action] - Owner: [Name] - Due: [Date]
- [ ] [Action] - Owner: [Name] - Due: [Date]
```

## Quick Reference

### Essential Commands

```bash
# Quick cluster health
kubectl get nodes && kubectl get pods -A | grep -v Running

# Service status
kubectl get pods,svc,endpoints -l app=<service>

# Recent events
kubectl get events --sort-by='.lastTimestamp' | tail -20

# Quick logs
kubectl logs -l app=<service> --tail=50 --all-containers
```

### Escalation Contacts

Document your escalation path:
1. Primary on-call
2. Secondary on-call
3. Team lead
4. Engineering manager
5. VP Engineering (SEV1 only)

## Related Skills

- **k8s-debug**: For Kubernetes-specific debugging
- **log-analysis**: For log pattern analysis
- **argocd-gitops**: For GitOps rollbacks
