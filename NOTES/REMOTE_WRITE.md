# Documentation

After we have the Virtual Machine and the service installed itself it is time to get some data.
The chosen approach was to use the Remote Write functionallity of Prometheus itself. With that
we can keep current monitoring implementation and get the same data without extra difficult
setup from our side. We use the following documentation and resources:

* Victoria Metrics Prom remote: https://docs.victoriametrics.com/#prometheus-setup
* Prometheus remote write explain: https://prometheus.io/docs/specs/remote_write_spec/
* Remote write tunning: https://prometheus.io/docs/practices/remote_write/
* Prometheus config: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write
