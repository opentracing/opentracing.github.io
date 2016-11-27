# Supported Tracer Implementations

## Zipkin and Jaeger

Zipkin and its cousin, Jaeger, both support OpenTracing in various languages. There is also an experimental [bridge from Brave (Zipkin Java) instrumentation to OpenTracing](https://github.com/openzipkin/brave-opentracing). Links: [zipkin-go-opentracing](https://github.com/openzipkin/zipkin-go-opentracing), [jaeger-client-java](https://github.com/uber/jaeger-client-java), [jaeger-client-go](https://github.com/uber/jaeger-client-go), [jaeger-client-python](https://github.com/uber/jaeger-client-python), and [jaeger-client-node](https://github.com/uber/jaeger-client-node).


## Appdash

Appdash ([background reading](https://sourcegraph.com/blog/announcing-appdash-an-open-source-perf-tracing/)) is a lightweight, Golang-based distributed tracing system, originally developed and since open-sourced by [sourcegraph](https://sourcegraph.com/). There is an OpenTracing-compatible `Tracer` implementation that uses Appdash as a backend; binding Appdash to OpenTracing instrumentation is trivial:

```go
import (
    "github.com/sourcegraph/appdash"
    appdashtracer "github.com/sourcegraph/appdash/opentracing"
)

func main() {
    // Initialization with a local collector:
    collector := appdash.NewLocalCollector(myAppdashStore)
    chunkedCollector := appdash.NewChunkedCollector(collector)
    tracer := appdashtracer.NewTracer(chunkedCollector)

    // Initialization with a remote collector:
    collector := appdash.NewRemoteCollector("localhost:8700")
    tracer := appdashtracer.NewTracer(collector)
}
```

For more details, read [the godocs](https://godoc.org/github.com/sourcegraph/appdash/opentracing).


## LightStep

[LightStep](http://lightstep.com/) runs a private beta with OpenTracing-native tracers in production environments. There are OpenTracing-compatible [LightStep Tracers](https://github.com/lightstep) available for Go, Python, Javascript, Objective-C, Java, PHP, Ruby, and C++.

## Hawkular

[Hawkular APM](http://www.hawkular.org/hawkular-apm/) supports OpenTracing-Java and has plans to support other platforms in the near future.

## Instana

[Instana](https://www.instana.com) provides an APM solution supporting [OpenTracing for Java](https://github.com/instana/instana-java-opentracing/blob/master/README.md), [Opentracing for NodeJs](https://github.com/instana/nodejs-sensor/blob/master/README.md#opentracing) and [Opentracing fo Go](https://github.com/instana/golang-sensor/blob/master/README.md). The Instana OpenTracing tracers are interoperable with the other Instana out of the box tracers for Java, Scala, NodeJs, PHP and Ruby.