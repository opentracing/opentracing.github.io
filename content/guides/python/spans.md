---
title: "Spans"
weight: 2
---

The OpenTracing Python API allows for only one span in a thread to be active at any point in time. There can be other spans involved with the same thread, which satisfy the following conditions:

- Started,
- Not finished,
- Not “active”.

There can be multiple spans on the same thread, if the spans are:

- Waiting for I/O,
- Blocked on a child Span or
- Off of the critical path

As it is inconvenient to pass an active Span from function to function manually, so OpenTracing requires that every Tracer contain a ScopeManager. A ScopeManager grants access to the “active” Span through a Scope that formalizes the activation and deactivation of a Span. The ScopeManager API allows for a span to be transferred to another thread or callback but not Scope.

Note that if a Scope exists when the developer creates a new Span then it will act as its parent, unless the programmer invokes ignoreActiveSpan() at buildSpan() time or specifies parent context explicitly.

## Accessing the Current Active Span
A developer can access any active span through a scope as follows:

```python
scope = tracer.scope_manager.active()
if scope is not None:
    scope.span.set_tag('...', '...')
```

## Moving a Span between Threads
Using the OpenTracing API, a developer can transfer the spans among different threads. A span’s lifetime might start in one thread and end in another. Passing of scopes to another thread or callback is not supported.

## Spans: Conventions and Standards

### Operation Names and Cardinality
Application and library developers need to specify the operation name of each span.
The operation name is general to a class of spans and represents a unique instance. The following is a statement for initializing a span with the operation name “say-hello” in Python:

`span = tracer.start_span(operation_name='someWork')`

The reason for choosing general operation names is to allow the tracing systems to do aggregations. For example, Jaeger tracer has an option of emitting metrics for all the traffic going through the application. Having a unique operation name for each span would make the metrics useless. For application or library developers who wish to capture the program arguments in the traces to distinguish them, the recommended solution is to annotate spans with tags or logs. These are discussed in the section below.

Each operation name should have some semantic meaning to it and should be chosen such that its cardinality is neither too high nor too low. The cardinality of an operation name is defined as follows:

`card(operation X) = total number of spans of operation X`

For example, when using http.url (as operation name) the cardinality would be too high, on the other hand if http.method is chosen as the operation name then the cardinality would be too low.


### Standard Tags
A tag is a key-value pair that provides certain metadata about the span defined by developers as part of instrumentation.  The tags describe attributes of the span that apply to the whole duration of the span. For example, if a span represents an HTTP request, then the URL of the request should be recorded as a tag because it remains constant throughout the lifetime of span.

Consider a program written for printing “Hello Bryan” to console. In this case, the string "Bryan" is a good candidate for a span tag, since it applies to the whole span and not to a particular moment in time. We can record it like this:

```python
with tracer.start_span('say-hello') as span:
        span.set_tag('hello-to', 'Bryan')
```

The OpenTracing Specification provides guidelines called Semantic Conventions for recommended tags.

### Granularity: Spans vs Logs
A log is similar to a regular log statement, it contains a timestamp and some data, but is associated with span from which it was logged. Logs are part of instrumentation done by application or library developers. For example, if the server responded with a redirect URL, the developer should log it since there is a clear timestamp associated with such event.

Again consider the same “Hello Bryan” program: we're formatting the hello_str and then printing it. Both of these operations take certain time, so we can log their completion:

```python
hello_str = 'Hello, %s!' % hello_to
span.log_kv({'event': 'string-format', 'value': hello_str})

print(hello_str)
span.log_kv({'event': 'println'})
```

The OpenTracing Specification provides guidelines called Semantic Conventions for log fields.

### What about log levels for spans?
The OpenTracing API does not specify log levels for spans, as these will differ for as per the needs of the application developer. However, it is possible for library developers to create their own API for adding log levels by wrapping OpenTracing API inside it and moulding it according to tracing needs.
