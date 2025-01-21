# VictoriaMetircs history
* VictoriaMetrics was founded in 2018 in Kyiv, Ukraine;
* First open source release - May 2.12.2018 (version v1.2.1);

# Why we need another monitoring tool for Time series?
Because ... why not?

[vm-faq](img/vm-faq.png)

# CNCF project from 12.07.2023
https://www.cncf.io/announcements/2023/07/12/over-30-new-members-join-the-cloud-native-computing-foundation/
[cncf-landscape-big](img/cncf-landscape-original.png)

If you ask where is VictoriaMetrics:
[cncf-landscape-zoom](img/cncf-landscape.png)

# Why they built it?
The founders reached the performance limits of Prometheus, particulary in terms of horizontal scalability. The idea was to create a whole new TSDB from scratch in Go, incorporating ideas from Clickhouse architecture (ClickHouse is a column oriented SQL database for analytical processing providing faster complex calculations).

VictoriaMetrics tries to be like a 'Prometheus but with ClickHouse architecture':
1. Advanced compression techniques that use less disk and less memory;
2. Stores and processes data in blocks for speed and efficiency;
3. Makes use of all avaliable CPU cores for maximum performance;

Also VictoriaMetircs tries to provide horizontal scalability.
