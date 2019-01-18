## Tracing

Added trace and span IDs to the Slf4J MDC, so you can extract all the logs from a given trace or span in a log aggregator, as shown in the following example logs:

```
2016-02-02 15:30:57.902  INFO [bar,6bfd228dc00d216b,6bfd228dc00d216b,false] 23030 --- [nio-8081-exec-3] ...
2016-02-02 15:30:58.372 ERROR [bar,6bfd228dc00d216b,6bfd228dc00d216b,false] 23030 --- [nio-8081-exec-3] ...
2016-02-02 15:31:01.936  INFO [bar,46ab0d418373cbc9,46ab0d418373cbc9,false] 23030 --- [nio-8081-exec-4] ...
```
Notice the `[appname,traceId,spanId,exportable]` entries from the MDC:

- `appname`: The name of the application that logged the span;

- `traceId`: The ID of the latency graph that contains the span (is a 64 bit number (`long`) sent in a hexadecimal form);

- `spanId`: The ID of a specific operation that took place (is a 64 bit number (`long`) sent in a hexadecimal form);

- `exportable`: Whether the log should be exported to Zipkin. When would you like the span not to be exportable? When you want to wrap some operation in a Span and have it written to the logs only.

Propagation mechanism (Sleuth) includes default logic to join a trace across HTTP or messaging boundaries. HTTP propagation works over Zipkin-compatible request headers.
Trace and span ids are extracted from Zipkin-compatible (B3) headers (either Message or HTTP headers), to start or join an existing trace. Trace information is injected into any outbound requests so the next hop can extract them.

The default way of coding tracing context is done via the `X-B3` HTTP headers: 

`X-B3-TraceId: 6bfd228dc00d216b, X-B3-SpanId: 46ab0d418373cbc9`

If the `X-B3-TraceId` and `X-B3-SpanId` HTTP headers are not present in the request, Sleuth generates values for these at the beginning of the request. By this way `TraceId` will be also the `SpanID` because this is the first span in the trace.

