# API Service

| Category     | SLI                                                                                   | SLO                                                                                                         |
|--------------|---------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Availability | Number of successful requests / total number of requests                              | 99%                                                                                                         |
| Latency      | Bucket of requests in a histogram showing the 90th percentile over the last 5 minutes | 90% of requests below 100ms                                                                                 |
| Error Budget | 1 - availability                                                                      | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   | Total number of successful requests over a period of time                             | 5 RPS indicates the application is functioning                                                              |
