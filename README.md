# LoomSuite Java Agent

## About This Project

This project is based on [OpenTelemetry Java Instrumentation](https://github.com/open-telemetry/opentelemetry-java-instrumentation) and extends it with additional functionality including:

- **Alibaba Extensions**: Custom instrumentations and extensions designed for Alibaba's ecosystem and cloud services
- **AI-related Instrumentation**: Enhanced support for AI/ML frameworks and services, including Generative AI semantic conventions
- **Custom Distribution Examples**: A comprehensive set of extension examples in the `examples/distro` directory

For the original OpenTelemetry project badges and community links:
[![Release](https://img.shields.io/github/v/release/open-telemetry/opentelemetry-java-instrumentation?include_prereleases&style=)](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/open-telemetry/opentelemetry-java-instrumentation/badge)](https://scorecard.dev/viewer/?uri=github.com/open-telemetry/opentelemetry-java-instrumentation)
[![Slack](https://img.shields.io/badge/slack-@cncf/otel--java-blue.svg?logo=slack)](https://cloud-native.slack.com/archives/C014L2KCTE3)

## LoomSuite Extensions

This project includes several extension points and examples that demonstrate how to extend OpenTelemetry Java instrumentation:

### Available Extensions in examples/distro

The `examples/distro` directory contains a comprehensive collection of extension examples:

- **[DemoIdGenerator](examples/distro/custom/src/main/java/com/example/javaagent/DemoIdGenerator.java)** - Custom `IdGenerator` implementation
- **[DemoPropagator](examples/distro/custom/src/main/java/com/example/javaagent/DemoPropagator.java)** - Custom `TextMapPropagator` for trace context propagation
- **[DemoSampler](examples/distro/custom/src/main/java/com/example/javaagent/DemoSampler.java)** - Custom `Sampler` for trace sampling decisions
- **[DemoSpanProcessor](examples/distro/custom/src/main/java/com/example/javaagent/DemoSpanProcessor.java)** - Custom `SpanProcessor` for span lifecycle management
- **[DemoSpanExporter](examples/distro/custom/src/main/java/com/example/javaagent/DemoSpanExporter.java)** - Custom `SpanExporter` for telemetry data export
- **[DemoResourceProvider](examples/distro/custom/src/main/java/com/example/javaagent/DemoResourceProvider.java)** - Custom resource attribute provider
- **[DemoAutoConfigurationCustomizerProvider](examples/distro/custom/src/main/java/com/example/javaagent/DemoAutoConfigurationCustomizerProvider.java)** - SDK auto-configuration customization

### AI and Generative AI Support

This project includes enhanced support for AI/ML frameworks:

- **Generative AI Semantic Conventions**: Located in `instrumentation-api-incubator/src/main/java/io/opentelemetry/instrumentation/api/incubator/semconv/genai/`
- **GenAI Metrics and Spans**: Specialized extractors and processors for AI workloads
- **Custom AI Instrumentations**: Future extensions for popular AI frameworks and services

### Alibaba Cloud Extensions

Planned extensions for Alibaba Cloud ecosystem integration (coming soon):

- Custom instrumentations for Alibaba Cloud services
- Enhanced support for Alibaba middleware and frameworks
- Specialized exporters for Alibaba observability platforms

* [About This Project](#about-this-project)
* [LoomSuite Extensions](#loomsuite-extensions)
* [About](#about)
* [Getting Started](#getting-started)
* [Configuring the Agent](#configuring-the-agent)
* [Supported libraries, frameworks, and application servers](#supported-libraries-frameworks-and-application-servers)
* [Creating agent extensions](#creating-agent-extensions)
* [Manually instrumenting](#manually-instrumenting)
* [Logger MDC auto-instrumentation](#logger-mdc-mapped-diagnostic-context-auto-instrumentation)
* [Troubleshooting](#troubleshooting)
* [Contributing](#contributing)

## About

This project is built on top of the OpenTelemetry Java instrumentation agent and provides all the same core functionality. The agent JAR can be attached to any Java 8+ application and dynamically injects bytecode to capture telemetry from a number of popular libraries and frameworks. You can export the telemetry data in a variety of formats and configure the agent and exporter via command line arguments or environment variables.

**Key Features:**

- **Full OpenTelemetry Compatibility**: All original OpenTelemetry Java instrumentation features are preserved
- **Extended Functionality**: Additional instrumentations and extensions for specialized use cases
- **Alibaba Ecosystem Support**: Enhanced support for Alibaba Cloud services and middleware
- **AI/ML Framework Support**: Specialized instrumentation for AI and machine learning workloads
- **Custom Distribution Examples**: Comprehensive examples for creating your own agent distributions

This project also publishes standalone instrumentation for several libraries (and growing) that can be used if you prefer that over using the Java agent. Please see the standalone library instrumentation column on [Supported Libraries](docs/supported-libraries.md#libraries--frameworks) for documentation on using those.

## Getting Started

Download
the [latest version](https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar).

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

### Maintainers

- [Lauri Tulmin](https://github.com/laurit), Splunk
- [Trask Stalnaker](https://github.com/trask), Microsoft

For more information about the maintainer role, see the [community repository](https://github.com/open-telemetry/community/blob/main/guides/contributor/membership.md#maintainer).

### Approvers

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

### Emeritus maintainers

- [Mateusz Rzeszutek](https://github.com/mateuszrzeszutek)
- [Nikita Salnikov-Tarnovski](https://github.com/iNikem)
- [Tyler Benson](https://github.com/tylerbenson)

For more information about the emeritus role, see the [community repository](https://github.com/open-telemetry/community/blob/main/guides/contributor/membership.md#emeritus-maintainerapprovertriager).

### Thanks to all of our contributors

<a href="https://github.com/open-telemetry/opentelemetry-java-instrumentation/graphs/contributors">
  <img alt="Repo contributors" src="https://contrib.rocks/image?repo=open-telemetry/opentelemetry-java-instrumentation" />
</a>

[config-agent]: https://opentelemetry.io/docs/zero-code/java/agent/configuration/

[config-sdk]: https://opentelemetry.io/docs/languages/java/configuration/

[manual]: https://opentelemetry.io/docs/languages/java/instrumentation/#manual-instrumentation

[suppress]: https://opentelemetry.io/docs/zero-code/java/agent/disable/
