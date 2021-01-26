# Monitoring

Any good monitoring solution is built on top of three pillars. Tracing, logging, and metrics. 

Tracing provides the most complete picture of the behavior of the underlying system. Tons of mostly unstructured messages are captured in plain text files, data bases or simply at the standard output stream of applications. Typically, tracing is not always enabled. It creates so much data that it is hard to swallow or to persist it somewhere. Even when the system encounters an error you will most likely not see it while viewing the trace messages flying around with the speed of light. 

In contrast to tracing, logging is always on. That is a good because you will never know when an error occurs, and you need to analyze. Sometimes it is hard to reproduce an issue and you cannot travel back in time to enable tracing. On the other hand, not every message which would be traced should be logged. Typically, only errors, warnings and some important information are logged. Herein lies a major benefit of logs. When somethings go wrong you might see it because some error logs are written. Before the costumer complains. 

Traces give much more details then logs but are not always on. The typical use case is to turn them on when a misbehavior has occurred, and the logs give not enough details to find the root cause. These days logs are often written as structured messages in contrast to traces. This means that not only native human readable text is logged but also objects like json. Such messages can then be processed not only by humans, but also by other processes. If an application would log each request duration using structured logging another monitoring application could read this log and present the average request duration for the last 10 minutes or so on a dashboard. 

This brings me to metrics. Structured or not, logs and traces provide contextual information about the execution of your software. You know which line of code produces which message under which circumstances. Metrics are different. They are somehow an aggerated view. A metric consists of a name, a value and some additional information. An example of a metric could be the HTTP_REQUEST_DURATION which depicts the request duration of a certain request. The value could be the duration in seconds e.g., 0.01s. An additional information is the route e.g., “/products”. Such metrics are often stored in time series databases which are designed for an efficient handling of such information. A time series is a collection of such data points together with their time.  

Metrics have now the great advantage that they can give you an overview of the current behavior of any system. Better as with logs and much better than with traces you can spot anomalies and degradations. Imagine a pilot of an airplane would need to consult the flight recorder instead of its instruments to get the current state of the airplane. Search the logs message for the height and speed would take too long. Fortunately he has a great dashboard in his cockpit. The air traffic control has also a dashboard but with another view of the same data. They have less detailed information but aggregated over lots of airplanes. 

Traces, logs and metrics are not fundamentally different. They provide different views on the same underlying data. Metrics give you an overview and should always be available. When you spot something suspicious you can investigate the logs. If still in doubt you can enable tracing. 

There are great tools on the market. For metrics my favorite is [Prometheus][1] for the aggregation and [Grafana][2] for the visualization. For logging I can suggest [Serilog][3]. [Jeager][4] is a good tool for distributed tracing.

[1]: https://prometheus.io/
[2]: https://grafana.com/
[3]: https://serilog.net/
[4]: https://www.jaegertracing.io/
