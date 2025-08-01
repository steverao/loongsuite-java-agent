# Loongsuite Java Agent

[![Release](https://img.shields.io/github/v/release/steverao/loongsuite-java-agent?include_prereleases&style=)](https://github.com/steverao/loongsuite-java-agent/releases/)
[![Slack](https://img.shields.io/badge/slack-@cncf/otel--java-blue.svg?logo=slack)](https://cloud-native.slack.com/archives/C014L2KCTE3)

* [About](#about)
* [Planned Extensions](#planned-extensions)
* [Getting Started](#getting-started)
* [Configuring the Agent](#configuring-the-agent)
* [Supported libraries, frameworks, and application servers](#supported-libraries-frameworks-and-application-servers)
* [Creating agent extensions](#creating-agent-extensions)
* [Example Extensions](#example-extensions)
* [Manually instrumenting](#manually-instrumenting)
* [Logger MDC auto-instrumentation](#logger-mdc-mapped-diagnostic-context-auto-instrumentation)
* [Troubleshooting](#troubleshooting)
* [Contributing](#contributing)
* [Maintainers](#maintainers)

## About

The Loongsuite Java Agent is an enhanced distribution of the [OpenTelemetry Java Instrumentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation) agent that provides additional capabilities for enterprise environments, particularly focused on Alibaba ecosystem integrations and AI-related instrumentation.

This project provides a Java agent JAR that can be attached to any Java 8+
application and dynamically injects bytecode to capture telemetry from a
number of popular libraries and frameworks.
You can export the telemetry data in a variety of formats.
You can also configure the agent and exporter via command line arguments
or environment variables. The net result is the ability to gather telemetry
data from a Java application without code changes.

This repository also publishes standalone instrumentation for several libraries (and growing)
that can be used if you prefer that over using the Java agent.
Please see the standalone library instrumentation column
on [Supported Libraries](docs/supported-libraries.md#libraries--frameworks).
If you are looking for documentation on using those.

## Planned Extensions

The Loongsuite Java Agent extends the OpenTelemetry Java Instrumentation with:

- **Alibaba Ecosystem Extensions**: Enhanced instrumentation for Alibaba middleware and services including:
  - Alibaba Cloud services integration
  - Dubbo framework enhancements
  - AliCloud observability improvements

- **AI-related Instrumentation**: Specialized instrumentation for AI and machine learning workloads:
  - AI model inference tracking
  - ML pipeline observability
  - GPU resource monitoring
  - AI service performance metrics

- **Enterprise Features**: Additional capabilities for enterprise deployment scenarios

## Getting Started

Download
the [latest version](https://github.com/steverao/loongsuite-java-agent/releases/latest/download/opentelemetry-javaagent.jar).

This package includes the instrumentation agent as well as
instrumentations for all supported libraries and all available data exporters.
The package provides a completely automatic, out-of-the-box experience.

Enable the instrumentation agent using the `-javaagent` flag to the JVM.

```
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -jar myapp.jar
```

By default, the OpenTelemetry Java agent uses the
[OTLP exporter](https://github.com/open-telemetry/opentelemetry-java/tree/main/exporters/otlp)
configured to send data to an
[OpenTelemetry collector](https://github.com/open-telemetry/opentelemetry-collector/blob/main/receiver/otlpreceiver/README.md)
at `http://localhost:4318`.

Configuration parameters are passed as Java system properties (`-D` flags) or
as environment variables. See [the configuration documentation][config-agent]
for the full list of configuration items. For example:

```
java -javaagent:path/to/opentelemetry-javaagent.jar \
     -Dotel.resource.attributes=service.name=your-service-name \
     -Dotel.traces.exporter=zipkin \
     -jar myapp.jar
```

## Configuring the Agent

The agent is highly configurable! Many aspects of the agent's behavior can be
configured for your needs, such as exporter choice, exporter config (like where
data is sent), trace context propagation headers, and much more.

For a detailed list of agent configuration options, see the [agent configuration docs][config-agent].

For a detailed list of additional SDK configuration environment variables and system properties,
see the [SDK configuration docs][config-sdk].

*Note: Config parameter names are very likely to change over time, so please check
back here when trying out a new version!
Please [report any bugs](https://github.com/open-telemetry/opentelemetry-java-instrumentation/issues)
or unexpected behavior you find.*

## Supported libraries, frameworks, and application servers

We support an impressively huge number
of [libraries and frameworks](docs/supported-libraries.md#libraries--frameworks) and
a majority of the most
popular [application servers](docs/supported-libraries.md#application-servers)...right out of the
box!
[Click here to see the full list](docs/supported-libraries.md) and to learn more about
[disabled instrumentation](docs/supported-libraries.md#disabled-instrumentations)
and how to [suppress unwanted instrumentation][suppress].

## Creating agent extensions

[Extensions](examples/extension/README.md) add new features and capabilities to the agent without
having to create a separate distribution or to fork this repository. For example, you can create
custom samplers or span exporters, set new defaults, and embed it all in the agent to obtain a
single jar file.

## Creating an agent distribution

[Distribution](examples/distro/README.md) provides guidance on creating a separate distribution, serving as a collection of examples for extending the functionality of the OpenTelemetry Java instrumentation agent. It also demonstrates how to repackage the agent while incorporating custom features.
[Agent extensions](#creating-agent-extensions) are recommended instead for most users as they are simpler and do not require rebuilding with each OpenTelemetry Java agent release.

## Example Extensions

The [`examples/distro`](examples/distro/) directory contains practical examples of how to extend the agent functionality:

### Core Extensions
- **[DemoIdGenerator](examples/distro/custom/src/main/java/com/example/javaagent/DemoIdGenerator.java)** - Custom `IdGenerator` for generating trace and span IDs
- **[DemoPropagator](examples/distro/custom/src/main/java/com/example/javaagent/DemoPropagator.java)** - Custom `TextMapPropagator` for context propagation
- **[DemoSampler](examples/distro/custom/src/main/java/com/example/javaagent/DemoSampler.java)** - Custom `Sampler` for trace sampling decisions
- **[DemoSpanProcessor](examples/distro/custom/src/main/java/com/example/javaagent/DemoSpanProcessor.java)** - Custom `SpanProcessor` for span lifecycle management
- **[DemoSpanExporter](examples/distro/custom/src/main/java/com/example/javaagent/DemoSpanExporter.java)** - Custom `SpanExporter` for telemetry data export

### Instrumentation Extensions
- **[DemoServlet3InstrumentationModule](examples/distro/instrumentation/servlet-3/src/main/java/com/example/javaagent/instrumentation/DemoServlet3InstrumentationModule.java)** - Additional instrumentation for servlet containers

These examples serve as templates for creating Alibaba-specific and AI-related instrumentations. You can use them as starting points for:
- Integrating with Alibaba Cloud services
- Adding AI/ML model monitoring capabilities
- Creating custom telemetry exporters for enterprise backends

## Manually instrumenting

For most users, the out-of-the-box instrumentation is completely sufficient and nothing more has to
be done. Sometimes, however, users wish to add attributes to the otherwise automatic spans,
or they might want to manually create spans for their own custom code.

For detailed instructions, see [Manual instrumentation][manual].

## Logger MDC (Mapped Diagnostic Context) auto-instrumentation

It is possible to inject trace information like trace IDs and span IDs into your
custom application logs. For details, see [Logger MDC
auto-instrumentation](docs/logger-mdc-instrumentation.md).

## Troubleshooting

To turn on the agent's internal debug logging:

`-Dotel.javaagent.debug=true`

**Note**: These logs are extremely verbose. Enable debug logging only when needed.
Debug logging negatively impacts the performance of your application.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## Maintainers

### Loongsuite Java Agent Maintainers

- [Steve Rao](https://github.com/steverao), Alibaba

### Upstream OpenTelemetry Java Instrumentation Maintainers

- [Lauri Tulmin](https://github.com/laurit), Splunk
- [Trask Stalnaker](https://github.com/trask), Microsoft

For more information about the maintainer role, see the [community repository](https://github.com/open-telemetry/community/blob/main/guides/contributor/membership.md#maintainer).

### Upstream Approvers

- [Gregor Zeitlinger](https://github.com/zeitlinger), Grafana
- [Jack Berg](https://github.com/jack-berg), New Relic
- [Jason Plumb](https://github.com/breedx-splk), Splunk
- [Jay DeLuca](https://github.com/jaydeluca)
- [Jean Bisutti](https://github.com/jeanbisutti), Microsoft
- [John Watson](https://github.com/jkwatson), Cloudera
- [Jonas Kunz](https://github.com/JonasKunz), Elastic
- [Steve Rao](https://github.com/steverao), Alibaba
- [Sylvain Juge](https://github.com/SylvainJuge), Elastic

For more information about the approver role, see the [community repository](https://github.com/open-telemetry/community/blob/main/guides/contributor/membership.md#approver).

### Upstream Emeritus Maintainers

- [Mateusz Rzeszutek](https://github.com/mateuszrzeszutek)
- [Nikita Salnikov-Tarnovski](https://github.com/iNikem)
- [Tyler Benson](https://github.com/tylerbenson)

For more information about the emeritus role, see the [community repository](https://github.com/open-telemetry/community/blob/main/guides/contributor/membership.md#emeritus-maintainerapprovertriager).

### Thanks to all of our contributors!

<a href="https://github.com/open-telemetry/opentelemetry-java-instrumentation/graphs/contributors">
  <img alt="Repo contributors" src="https://contrib.rocks/image?repo=open-telemetry/opentelemetry-java-instrumentation" />
</a>

[config-agent]: https://opentelemetry.io/docs/zero-code/java/agent/configuration/

[config-sdk]: https://opentelemetry.io/docs/languages/java/configuration/

[manual]: https://opentelemetry.io/docs/languages/java/instrumentation/#manual-instrumentation

[suppress]: https://opentelemetry.io/docs/zero-code/java/agent/disable/
