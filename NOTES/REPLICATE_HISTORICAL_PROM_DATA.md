# Replicate old historical data from Prometheus

After we incoming 'fresh' data is time to replicate the old historical data from Prometheus itself.
We use 'vmctl' tool from victoriametrics-tools package to replicate physical snapshots from Prometheus
TSDB directly to VictoriaMetrics instance. Check the following documentation:
* https://docs.victoriametrics.com/vmctl/
* https://docs.victoriametrics.com/vmctl/#migrating-data-from-prometheus
* https://medium.com/@romanhavronenko/victoriametrics-how-to-migrate-data-from-prometheus-d44a6728f043
