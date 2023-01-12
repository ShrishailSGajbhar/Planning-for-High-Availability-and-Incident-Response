## Availability SLI
### The percentage of successful requests over the last 5m
```
(sum (increase(apiserver_request_total{job="apiserver",status!~"5.."}[5m]))/sum (increase(apiserver_request_total{job="apiserver"}[5m])))
```

## Latency SLI
### 90% of requests finish in these times
```
histogram_quantile(0.90, sum(rate(prometheus_http_request_duration_seconds_bucket[5m])) by (le))
```   

## Throughput
### Successful requests per second
```
sum(rate(prometheus_http_requests_total{code=~"2.."}[5m]))
```

## Error Budget - Remaining Error Budget
### The error budget is 20%
```
( 1 - ((1 - (sum(increase(prometheus_http_requests_total{code="200"}[7d])) by (verb)) / sum(increase(prometheus_http_requests_total[7d])) by (verb)) / (1 - .80)) )
```
