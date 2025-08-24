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

## Production SLOs

| SLI | Objective | Measurement Window | Error Budget |
|-----|-----------|-------------------|--------------|
| Availability | ≥ 99.5% | Monthly (30 days) | 0.5% (3.6 hours/month) |
| Latency (95th percentile) | ≤ 100ms | Monthly | 5% of requests > 100ms |
| Error Rate | ≤ 0.1% | Weekly | 0.1% error budget |
| Throughput | ≥ 100 RPS | Peak hours | N/A (capacity planning) |

## Development SLOs

| SLI | Objective | Measurement Window | Error Budget |
|-----|-----------|-------------------|--------------|
| Availability | ≥ 99.0% | Weekly | 1% (1.68 hours/week) |
| Latency (95th percentile) | ≤ 200ms | Weekly | 5% of requests > 200ms |
| Error Rate | ≤ 1% | Daily | 1% error budget |

## Monitoring

- **Availability**
```promql
(
  sum(rate(http_requests_total{job="dumbkv"}[30d])) -
  sum(rate(http_requests_total{job="dumbkv", status=~"5xx"}[30d]))
) / sum(rate(http_requests_total{job="dumbkv"}[30d])) * 100
```

- **Latency (95th percentile)**
```promql
histogram_quantile(0.95, 
  sum(rate(http_request_duration_seconds_bucket{job="dumbkv"}[5m])) by (le)
)
```

- **Error Rate**
```promql
sum(rate(http_requests_total{job="dumbkv", status=~"4xx|5xx"}[5m])) /
sum(rate(http_requests_total{job="dumbkv"}[5m])) * 100
```