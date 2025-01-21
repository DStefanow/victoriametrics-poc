Structure of presentation:

1. [Introduction slide regarding time series data](time_series.md):
	1. What is time series?
	2. Why there are useful?
	3. How there are stored?
2. [Introduction slide regarding Prometheus](prometheus.md):
	1. What is Prometheus?
	2. History and development of Prometheus?
3. [What problems we have with metrics?](prometheus_problems.md)
	1. Huge usage of resources - RAM, CPU and disk;
	2. Not to easy maintenance because of single binary setup;
	3. Realy big WAL restore time;
	4. Cardinality problem;
	5. etc.;
4. [Introduction regarding VictoriaMetrics?](victoriametrics.md)
	1. Why we have and what problems try to resolve?
	2. History and development;
	3. Resource usage;
	4. Data compaction;
	5. Cluster specification - we have different tools/binaries for every component;
	6. Flexible configuration;
	7. Fast restore after restart;
5. How to migrate from Prometheus to VictoriaMetrics?
	1. Migration with the help of Remote Write;
	2. Single VictoriaMetrics - support Prometheus scrape config;
	3. vmagent;
	4. Migrating historical data with vmctl;
6. Migrating recording/alerting rules;
7. Prometheus vs. VictoriaMetircs benchmark:
	1. CPU;
	2. RAM;
	3. Disk;
	4. Bandwidth;
8. Some extra tools (cluster setup and more):
	1. vminsert;
	2. vmstorage;
	3. vmbackup;
	4. vmrestore;
	5. Helm charts;
9. Interface walkthrough:
	1. vmui;
	2. vmalert;
	3. targrts;
	4. configuration;
	5. helpers;
10. Used resources and references.

