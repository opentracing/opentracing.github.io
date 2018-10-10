---
title: "Loki: An OpenSource Zipkin / Prometheus Mashup, Written in Go"
videoUrl: https://www.youtube.com/embed/Bmzx-5uExPM
thumbnail: https://img.youtube.com/vi/Bmzx-5uExPM/maxresdefault.jpg
layout: video
featured: false
date: 2017-03-29
---

In this talk, Tom Wilkie presents a prototype OpenTracing implementation dubbed "Loki". Loki is a Zipkin-compatible distributed tracer written in Go. Unlike Zipkin, Loki pulls traces from the application under examination by reusing Prometheus' service discovery and scraping code. This presents some unique advantages over traditional distributed tracers as the tracer knows the identify of the services under examination, and helps present a consistent naming topology when used with Prometheus.
