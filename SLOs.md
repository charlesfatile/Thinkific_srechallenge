# DumbKV Service Level Objectives (SLOs)

## Overview
DumbKV is a simple key-value store API running on Kubernetes.  
SLOs define the expected reliability and performance of the service from the user’s perspective.  
They help us measure **if the service is “good enough”**.

---

## Availability SLO
- **Objective**: The service should be available for **99.5%** of requests over a 30-day rolling window.  
- **Reasoning**: DumbKV is not a business-critical database, but downtime should be minimal.

- **SLI (Service Level Indicator)**:
    * Ratio of successful requests (`HTTP 2xx`) vs total requests.

**Prometheus Query**:
```promql
sum(rate(http_requests_total{status=~"2.."}[5m]))
/
sum(rate(http_requests_total[5m]))
```

## Latency SLO

- **Objective**: 95% of requests should complete within 250ms.

- **Reasoning**: Users expect near-instant responses from a key-value store.

## Data Durability SLO

- **SQLite backend**: Durability is limited (single-node PVC).

- **Postgres backend**: RPO (Recovery Point Objective) of 5 minutes with regular backups.

