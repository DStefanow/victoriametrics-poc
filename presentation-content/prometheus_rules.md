# We have the metrics, now it's time to setup the monitoring

When we have all the data we can continue with the setup of recording and alerting rules. VictoriaMetrics has own query language - MetricsQL, but again it is totally base on PromQL, so we can copy again all recording and alerting rules and have everything needed.

VictoriaMetrics single host binary does not provide recording and alerting implementation itselft. For that purpose we need to use **vmalert** which is a very small binary with fully functional alerting implementation. How it looks like:

```
./vmalert -rule=/path/to/rules/*.yaml \
  -datasource.url=http://victoria-metrics:8428 \
  -notifier.url=http://alertmanager:9093 \
  -remoteWrite.url=http://victoria-metrics:8428 \
  -remoteRead.url=http://victoria-metrics:8428 \
  -evaluationInterval=1m
```
