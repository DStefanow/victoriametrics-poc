# Kubernetes cluster

1. Scraping Kubernetes metrics with vmagent:
	For scraping Kubernetes cluster we can use vmagent Helm chart:
	* https://docs.victoriametrics.com/vmagent/
	* https://docs.victoriametrics.com/helm/victoriametrics-agent/
	* https://docs.victoriametrics.com/guides/k8s-monitoring-via-vm-cluster/#3-install-vmagent-from-the-helm-chart

	Currently we have a problem with the version of Kubernetes 1.19. The Helm chart for vmagent does not support
version lower than 1.23 we will stick up with Prometheus Remote Write for now. We will try to migrate some external targets to VictoriaMetrics itself. We will drop all metrics except the ones from Prometheus operator itself.

2. Setup scrape from VictoriaMetircs itself for all posible targets outside Kubernetes cluster:
	1. We can get the scrape config directly from the Prometheus pod and try to replicate it on VictoriaMetrics - The following configuration is about 43k line and it will be better to replicate them directly from the interface or ConfigMap one by one;
	2. The other option is to get it from the interface for all posible targets;

3. Rewrite Remote Write option in Prometheus to only send data from Prometheus Kubernetes operator;
