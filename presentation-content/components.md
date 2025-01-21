# Components

Most important and cool stuff regarding VictoriaMetrics is multitenancy architecture and we can easy create a cluster from the different components.

## Scrape agent - vmagent

Helps scrape metrics from different data sources and store them in different endpoints with Remote write protocol (both Prometheus and VictoriaMetrics way).

## Alerting agent - vmalert

Support MetricsQL language and it is easy to setup.

## Cluster components

### vminsert

Responsible to accept ingested data and distribute it amound **vmstorage** nodes. Support various protocols like Prometheus, VictoriaMetrics remote write protocol, InfluxDB agent, DataDog, OpenTelemetry and etc.

### vmstorage

Responsible for storage and managing time series data. It is designed to store raw data and return queried data within given time range.

### vmselect

Executes queries and retrieve data from **vmstorage** nodes.

## Other tools

### vmauth

HTTP proxy which can authorize, route and load balance requests across VictoriaMetrics component.

### vmbackup and vmrestore

Creates and restore VictoriaMetrics backups from instant snapshots.

### vmgateway

### vmbackupmanager (Entrerprise package)

Automate regular backup procedures.

### vmgateway (Enterprise package)

Provides Rate Limiter and Token Access Control.

### vmanomaly (Enterprise package)

Detecting 'abnormal' behavior in time-series data.
