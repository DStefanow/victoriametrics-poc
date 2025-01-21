# Benchmark

## How our setup is organized

We scrape a lot of different kind of application and tools:
1. Kubernetes cluster with a lot of services in it;
2. A lot virtual machines - node exporter;
3. PostgreSQL - postgres_exporter;
4. ClickHouse - clickhouse_exporter;
5. Redis - redis_exporter;
6. ScyllaDB - built-in metrics endpoint;
7. ELK stack - logstash and elasticsearch exporters;
8. RabbitMQ - built-in metrics endpoint;
9. Artifactory - artifactory exporter;
10. Jenkins - jenkins Prometheus plugin;
11. A lot of other custom targets for monitoring.

Retention time - 30 days (1 month);
Active Time series in Head ~ 4M;

Prometheus resource usage:
* Average memory usage for the last 72 hours ~ 35GB

```
avg_over_time(process_resident_memory_bytes{job="prometheus"}[72d]) / 1024 ^ 3
```

* Average CPU usage for the last 72 hours ~ 7.2 cores

```
avg_over_time(rate(process_cpu_seconds_total{job="prometheus"}[5m])[72d:5m])
```

* Maximum CPU cores for the last 72 hours ~ 10 cores

```
max_over_time(rate(process_cpu_seconds_total{job="kube-prometheus-stack-prometheus"}[5m])[72d:5m])
```

* Used disk size with data retention of 30 days - 450GB

## Benchmark after migration to VictoriaMetrics

*

* Average memory usage for the last 72 hours ~ 14GB

```
avg_over_time(process_resident_memory_bytes{job=""}[72d]) / 1024 ^ 3
```

* Average CPU usage for the last 72 hours ~ < 1 core

```
avg_over_time(rate(process_cpu_seconds_total{job="prometheus"}[5m])[72d:5m])
```

* Maximum CPU cores for the last 72 hours ~ 6 cores

```
max_over_time(rate(process_cpu_seconds_total{job="kube-prometheus-stack-prometheus"}[5m])[72d:5m])
```

* Used disk size with data retention of 30 days - 285GB

Compared with Prometheus we have:
* 60% less memory usage;
* 80% less average cpu usage;
* 37% less disk space for data;

## Bytes per data point

VictoriaMetrics provides a lot of Grafana dashboard for self monitoring and alerting rules to monitor itself:
* https://victoriametrics.com/blog/victoriametrics-monitoring/
* https://grafana.com/orgs/victoriametrics/dashboards

One of the cool panels in VictoriaMetrics single node dashboard is 'Bytes per point' which shows us used bytes for one data point:

[bytes-per-data-original](img/bytes-per-data-original.png)

But sometimes we can see something like:

[bytes-per-data-bad](img/bytes-per-data-bad.png)

And before thinking that VictoriaMetrics shows false information in their benchmark. We should ask ourself what kind of data we scraping/migrating?

If we have data points like:
```
rate5m:cpu_usage{instance="172.17.206.32:9100", core="2", mode="idle"} 32.923713468383242 (with 14 digits after digital point)
```

It is posible to use more size than usual for a data which is not "rounded up" because of the behaviour of float64 data. All of that depends of our business requirement - Do we need all that digits after decimal point? If we are working with some science tools for sensetive measure it is possible, for financial operations also. But if we need to calculate how much CPU cores are in idle mode for the last 5 minutes maybe we can round some digits.

For migrating old data this is achiable with:

```
--vm-round-digits value Round metric values to the given number of decimal digits after the point. This option may be used for increasing on-disk compression level for the stored metrics (default: 100)
```
