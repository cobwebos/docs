---
title: 在任何环境中监视 Java 应用程序-Azure Monitor Application Insights
description: 适用于在任何环境中运行的 Java 应用程序的应用程序性能监视，无需检测应用。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: b9c1a52051e63beee9a784714a7bb1a6a79e8759
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687715"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java 无代码置备应用程序监视 Azure Monitor Application Insights-公共预览版

Java 无代码置备应用程序监视完全是简单的，无需更改代码，只需通过几次配置更改即可启用 Java 代理。

 Java 代理可在任何环境中工作，并允许你监视所有 Java 应用程序。 换句话说，无论你是在本地 Vm 上运行 Java 应用，在 AKS 上，在 Windows 上，在 Windows 上，在 Linux 中对其进行命名，而 Java 3.0 代理会监视应用。

不再需要将 Application Insights Java SDK 添加到应用程序，因为3.0 代理 autocollects 请求、依赖项和日志。

你仍可以从应用程序发送自定义遥测数据。 3.0 agent 将跟踪该代理，并将其与所有 autocollected 遥测相关联。

## <a name="quickstart"></a>快速入门

**1. 下载代理**

下载[applicationinsights-agent-3.0.0-preview](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. 将 JVM 指向代理**

添加`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar`到应用程序的 JVM 参数

典型的 JVM 参数`-Xmx512m`包括`-XX:+UseG1GC`和。 如果您知道在何处添加它们，则您已经知道要将此添加到何处。

有关配置应用程序的 JVM 参数的其他帮助，请参阅[3.0 预览版：更新 JVM 参数的技巧](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments)。

**3. 将代理指向 Application Insights 资源**

如果还没有 Application Insights 资源，可以按照[资源创建指南](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)中的步骤创建一个新的资源。

通过设置环境变量，将代理指向 Application Insights 资源：

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

或者，通过创建一个名为`ApplicationInsights.json`的配置文件，并将其放在`applicationinsights-agent-3.0.0-PREVIEW.3.jar`与相同的目录中，并提供以下内容：

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

可以在 Application Insights 资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 连接字符串":::

**4. 就这么简单！**

现在启动应用程序，并访问 Azure 门户中的 Application Insights 资源以查看监视数据。

> [!NOTE]
> 监视数据可能需要几分钟的时间才能在门户中显示。


## <a name="configuration-options"></a>配置选项

在该`ApplicationInsights.json`文件中，你还可以配置：

* 云角色名称
* 云角色实例
* 应用程序日志捕获
* JMX 指标
* Micrometer
* 检测信号
* 采样
* HTTP 代理
* 自行诊断

请参阅[3.0 公共预览版：配置选项](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config)中的详细信息。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Autocollected 请求、依赖项、日志和指标

### <a name="requests"></a>Requests

* JMS 使用者
* Kafka 使用者
* Netty/WebFlux
* Servlet
* 弹簧计划

### <a name="dependencies-with-distributed-trace-propagation"></a>与分布式跟踪传播的依赖项

* Apache HttpClient 和 HttpAsyncClient
* gRPC
* HttpURLConnection
* JMS
* Kafka
* Netty 客户端
* OkHttp

### <a name="other-dependencies"></a>其他依赖项

* Cassandra
* JDBC
* MongoDB （异步和同步）
* Redis （番茄酱和 Jedis）

### <a name="logs"></a>日志

* util。日志记录
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>指标

* Micrometer
* JMX 指标

## <a name="sending-custom-telemetry-from-your-application"></a>从应用程序发送自定义遥测数据

3.0 + 中的目标是允许使用标准 Api 发送自定义遥测。

我们支持 Micrometer、OpenTelemetry API 和常用日志记录框架。 Application Insights Java 3.0 将自动捕获遥测数据，并将其与所有 autocollected 遥测相关联。

出于此原因，我们目前并不打算发布带有 Application Insights 3.0 的 SDK。

Application Insights Java 3.0 已在侦听发送到 Application Insights Java SDK 2.x 的遥测数据。 此功能是适用于现有的2.x 用户的升级案例的重要组成部分，它在 OpenTelemetry API 正式发布之前，在自定义遥测支持中填充了重要的间隔。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用 Application Insights Java SDK 2.x 发送自定义遥测数据

将`applicationinsights-core-2.6.0.jar`添加到应用程序（Application Insights Java 3.0 支持所有2.x 版本，但如果有选择，则可以使用最新版本）：

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

创建 TelemetryClient：

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

并使用它来发送自定义遥测数据。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>指标

可以通过[Micrometer](https://micrometer.io)发送指标遥测数据：

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

也可以使用 Application Insights Java SDK 2.x：

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>依赖项

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>日志
可以通过常用的日志记录框架发送自定义日志遥测。

也可以使用 Application Insights Java SDK 2.x：

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外
可以通过常用的日志记录框架发送自定义异常遥测。

也可以使用 Application Insights Java SDK 2.x：

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>从 Application Insights Java SDK 2.x 升级

如果已在应用程序中使用 Application Insights Java SDK 2.x，则无需将其删除。 Java 3.0 代理将检测它，并捕获并关联通过 Java SDK 2.x 发送的任何自定义遥测，同时禁止 Java SDK 2.x 执行的任何 autocollection，以防止重复捕获。

> [!NOTE]
> 注意：使用3.0 代理时，Java SDK 2.x TelemetryInitializers 和 TelemetryProcessors 将不会运行。
