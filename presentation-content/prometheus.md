# What is Prometheus?

* Prometheus is an open-source system monitoring and alerting toolkit originally built at SoundCloud (2012);
* Second hosted project by Cloud Native Computing Foundation after Kubernetes (2016);
* Prometheus collects and stores its metrics at time series data. Metrics information is stored with the timestamp at which it was recorded, alongside optional key-value pairs called labels;

# Key concepts
1. Metric - numeric measure or observation of something. Example metric name:
	1. requests_total: Total requests to given API;
	2. node_memory_MemAvailable_bytes: Avaliable bytes for given instance;
	3. node_cpu_seconds_total: CPU time in seconds for given instance;
2. Time series - combination between metric name and labels (**additional meta-information in the form of key/value pairs**):
	1. requests_total{path="/login", status="2xx"};
	2. node_memory_MemAvailable_bytes{instance="172.17.1956.35:9100"}
	3. node_cpu_seconds_total{instance="172.17.1956.35:9100", cpu="0", mode="user"};
3. Raw samples - Every unique time series may consist of an arbitary number of **value, timestamp** data point (a.k.a. raw sample):
'''
requests_total{path="/", code="2xx"} 321 1737381556000 # UNIX timestamp with milisecond precision
'''
4. Cardinality - The number of unqiue time series (metric name and label combination);
5. Different type of metrics:
	1. Counter is a metric which value increases or stays the same over time. It cannot decrease in general case. The only exception is 'counter reset' when the metric is set back to zero. Example: requests_total and node_cpu_seconds_total;
	2. Gauge - measuring data that can go up and down over time. Example: node_memory_MemAvailable_bytes;
	3. Histogram and summaries - More complex TS structures where we can store and extract information for a set of counters in given range and use it with φ-quantiles functions measuring SLO for given uptime/response time and etc.
