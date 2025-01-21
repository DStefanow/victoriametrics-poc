# Actual transfer

## Prometheus Remote Write Protocol
The most common way to migrate from one monitoring system to another is to create the separate system, setup it and start scraping and agregating the data.
One possible way is to use Prometheus Remote Write Protocol. VictoriaMetrics supports it totally and we can easy setup our instance and just add a remote write protocol. We are going to have all **live** data from Prometheus directly to VictoriaMetrics.

## Scrape configuration
The remote write protocol is not a bad solution but also increase between 10-25% RAM usage of the instance. So we can move to the other part of the setup. VictoriaMetrics
single node has the same configuration option for scraping as Prometheus, so we can just copy/paste the configuration:
```
/usr/local/bin/victoria-metrics-prod -storageDataPath=/var/lib/victoria-metrics \
	-promscrape.config=/usr/local/etc/prometheus/scrape-config.yaml \
	-retentionPeriod=30d \
	-selfScrapeInterval=10s \
	--vmalert.proxyURL=http://localhost:8880
```
And scrape config looks like:
```yaml
# VMAlert (self hosted)
- job_name: vmalert
  static_configs:
  - targets:
    - localhost:8880

# Clickhouse
# Clickhouse host node metrics
- job_name: clickhouse-hostnode
  static_configs:
    - targets:
      - http://172.17.197.39:9100/metrics
      - http://172.17.197.40:9100/metrics
      - http://172.17.197.49:9100/metrics
      - http://172.17.197.89:9100/metrics
      - http://172.17.196.191:9100/metrics
      - http://172.17.196.193:9100/metrics
```

## vmagent
vmagent is a component that supports scraping metrics from various sources and perform some 'extra' work like relabeling and filtering collected metrics:

[vmagent](img/vmagent.webp)

The question now is why we need to use vmagent instead of Prometheus itself?
* Regarding documentation:

[why-vmagent](img/why-vmagent.png)
VictoriaMetrics remote write protocol reduce the network bandwidth 2x-5x (based on documentation) than Prometheus RWP. Also when the remote storage is temporary unavaliable buffers the incoming data to the disk.

* And again ... it uses less amount of RAM and CPU than Prometheus.

## But what about if we want to migrate old historical data from Prometheus?

We can have the case where we don't have the time to wait to have the same amount of data points back in time as Prometheus. For example we need to to perform a benchmark between Prometheus and VictoriaMetrics and we have 30 days but the retention time of Prometheus is 3 months (90 days). Or the most valid case is that we have a lot of historical data we need and want to store it. Then how we can perform the migration. For that case we can use vmctl:

```
$ /usr/local/bin/vmctl-prod prometheus --help
NAME:
   vmctl prometheus - Migrate time series from Prometheus

USAGE:
   vmctl prometheus [command options]
```

For that case we need a snapshot of Prometheus TSDB. We can achieve that we enabled Admin API:

* https://www.robustperception.io/taking-snapshots-of-prometheus-data/

Prometheus snapshot is a fast operation because and does not require more space, because it does not create a copy of current TSDB files. Instead of that create a hardlinks to current data. Every Prometheus snapshots contains blocks grouped in 2 days data. With that we can perform partial migration of data. Let says we need data only for few days and we can use that blocks. Also we can filter data by labels with **--prom-filter-label value** if we want to filter specific metric or metric set containing given label. vmctl default uses 2 paralel workers. If we have enough cores we can use **--vm-concurrency** to speed up the procedure. The output of command looks like that:

```
    $ /usr/local/bin/vmctl-prod  prometheus --prom-snapshot snapshot/
    Prometheus import mode
    Prometheus snapshot stats:
      blocks found: 1;
      blocks skipped by time filter: 0;
      min time: 1734048000001 (2024-12-13T00:00:00Z);
      max time: 1734242400000 (2024-12-15T06:00:00Z);
      samples: 21119627328;
      series: 4088432.
    Found 1 blocks to import. Continue? [Y/n] Y
    VM worker 0:↘ 1154296 samples/s
    VM worker 1:↘ 1152165 samples/s
    Processing blocks: 0 / 1

```
