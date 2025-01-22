# Problems with Prometheus

## Resource usage
With every new target,recording and alerting rule that we are adding we increase resource usage of Prometheus for **4438520** data points we are using:
* 36GB of memory;
* About 8 CPU cores;
* 450GB storage (for 30 day retention);
The resource usage increase proportional for every entry that we are adding it;

## Not so easy for maintenance
Prometheus has a single binary architecture and every time when we need to perform some command line option change we need to restart the process/Kubernetes deployment and our monitoring tool is down for given time (sometimes is longer that we expected - check in the next option). Yes, we have solutions like Thanos, but here we talk about simple Promethues instance;

## What about a restart?
[too_long](img/too_long.gif)

When we need to perform a service restart we should check last WAL (Write ahead log) compaction on the disk. Why? Because it gonna take hell of a time to restore all chunks from WAL. Write ahead log is a functionallity which give us the abillity to keep current or near past data in the memory before it is added to disk segments. The problem cames when we need to perform service restore. The WAL restore time for every different chunk can take a lot of time:
```
prometheus[3506]: level=info ts=2023-07-04T09:27:50.126Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91024 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.126Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91023 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.125Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91022 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.125Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91021 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.123Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91020 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.123Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91019 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.123Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91018 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.121Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91017 maxSegment=93088
prometheus[3506]: level=info ts=2023-07-04T09:27:50.120Z caller=head.go:577 component=tsdb msg="WAL segment loaded" segment=91016 maxSegment=93088
```

And when we scraping a lot of metrics with huge cardinality it is better to perform service restart exactly after WAL write because we have fewer segments for restore.


## Cardinality problem
As we can see from the above point when we have a huge cardinality we have a huge problem. Not only the restart but the resources needs more expand regarding CPU, memory and disk. The reason behind that is that - Prometheus keeps a cache of present time series ids in memory and when we receive a new data is easy to update it. But if we have a new entry for TS we need to insert it which is a expensive operation. This is why we don't want labels like user_ids, sessions, paths with 3+ combinations and etc. in our TSDB.
