# Replicate old historical data from Prometheus

After we incoming 'fresh' data is time to replicate the old historical data from Prometheus itself.
We use 'vmctl' tool from victoriametrics-tools package to replicate physical snapshots from Prometheus
TSDB directly to VictoriaMetrics instance. Check the following documentation:
* https://docs.victoriametrics.com/vmctl/
* https://docs.victoriametrics.com/vmctl/#migrating-data-from-prometheus
* https://medium.com/@romanhavronenko/victoriametrics-how-to-migrate-data-from-prometheus-d44a6728f043
* Enable TSDB storage from Prometheus: https://www.robustperception.io/taking-snapshots-of-prometheus-data/

# Procedure

1. First we need to enable Prometheus Admin API to allow creating calls for TSDB snapshots:
	1. https://prometheus.io/docs/prometheus/latest/querying/api/#tsdb-admin-apis
	2. https://www.robustperception.io/taking-snapshots-of-prometheus-data/
	3. Helm chart: https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack/14.0.1?modal=values&path=prometheus.prometheusSpec.enableAdminAPI
2. After that we perform a CURL request to create a snapshot:
	* curl -X POST https://prometheus.sof1-dev.kube-infra.com/api/v1/admin/tsdb/snapshot
3. The following command will create a snapshot folder in your TSDB storage path:
	* Something like: snapshots/20250112T111808Z-29461a8c20ada2d3/ (snapshots/<YYYYMMddHHmmss>Z-<hash>
4. Then we can replicate it to our instance with simple copy;
5. Tor restore a snapshot we need to use vmcli command:
	* /usr/local/bin/vmctl-prod  prometheus --prom-snapshot snapshot/
6. The output should be something like:
"""
	Prometheus import mode
	Prometheus snapshot stats:
	  blocks found: 1;
	  blocks skipped by time filter: 0;
	  min time: 1734048000001 (2024-12-13T00:00:00Z);
	  max time: 1734242400000 (2024-12-15T06:00:00Z);
	  samples: 21119627328;
	  series: 4088432.
	Found 1 blocks to import. Continue? [Y/n]
"""

with a prompt
7. After you hit enter you are going to see the progress bar of the migration:
"""
	Found 1 blocks to import. Continue? [Y/n] Y
	VM worker 0:↘ 1154296 samples/s
	VM worker 1:↘ 1152165 samples/s
	Processing blocks: 0 / 1
"""
8. After that we can verify that the data is added in the storage and query some samples;
