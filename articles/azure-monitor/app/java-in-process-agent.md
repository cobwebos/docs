---
title: 在任何环境中监视 Java 应用程序 - Azure 监视器应用程序见解
description: 应用程序性能监视，适用于在任何环境中运行的 Java 应用程序，而无需检测应用程序。 分布式跟踪和应用程序映射。
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5a62be45320523ee0577d56eb557a4f87a58a1cc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886851"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java 无代码应用程序监视 Azure 监视器应用程序见解 - 公共预览

Java 无代码应用程序监视是简单性的 - 没有代码更改，Java代理只能通过几个配置更改来启用。

 Java 代理在任何环境中工作，并允许您监视所有 Java 应用程序。 换句话说，无论您是在 VM 上、本地还是 AKS、Windows、Linux 上运行 Java 应用，Java 3.0 代理都将监视你的应用。

不再需要将应用程序见解 Java SDK 添加到应用程序，因为 3.0 代理自行收集请求、依赖项和日志。

您仍然可以从应用程序发送自定义遥测。 3.0 代理将跟踪它与所有自动收集的遥测数据并关联。

## <a name="quickstart"></a>快速入门

**1. 下载代理**

下载[应用程序见解-代理-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

**2. 将 JVM 指向代理**

添加到`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.2.jar`应用程序的 JVM args

典型的 JVM args 包括`-Xmx512m`和`-XX:+UseG1GC`。 因此，如果您知道要在哪里添加这些，那么您已经知道在哪里添加此。

有关配置应用程序的 JVM args 的其他帮助，请参阅[3.0 预览：更新 JVM args 的提示](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)。

**3. 将代理指向您的应用程序见解资源**

如果还没有应用程序见解资源，则可以按照[资源创建指南](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)中的步骤创建新的资源。
创建名为 的`ApplicationInsights.json`配置文件，并将其放在与`applicationinsights-agent-3.0.0-PREVIEW.2.jar`的目录中，包含以下内容：

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

您可以在应用程序见解资源中找到连接字符串：

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="应用程序见解连接字符串":::

**4. 就这个了！**

现在启动应用程序，然后转到 Azure 门户中的应用程序见解资源以查看监视数据。

> [!NOTE]
> 监视数据可能需要几分钟才能显示在门户中。


## <a name="configuration-options"></a>配置选项

在文件中`ApplicationInsights.json`，您还可以另外配置：

* 云角色名称
* 云角色实例
* 应用程序日志捕获
* JMX 指标
* Micrometer
* 检测信号
* 采样
* HTTP 代理
* 自我诊断

请参阅[3.0 公共预览版的详细信息：配置选项](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options)。

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>自动收集的请求、依赖项、日志和指标

### <a name="requests"></a>Requests

* JMS 消费者
* 卡夫卡消费者
* 净/网络流
* Servlet
* 春季计划

### <a name="dependencies-with-distributed-trace-propagation"></a>具有分布式跟踪传播的依赖项

* 阿帕奇 HttpClient 和 HttpAsync 客户端
* gRPC
* java.net.HttpURL连接
* JMS
* Kafka
* 净客户端
* OkHttp

### <a name="other-dependencies"></a>其他依赖项

* Cassandra
* JDBC
* 蒙戈DB（异步和同步）
* 雷迪斯（莱图斯和绝地）

### <a name="logs"></a>日志

* java.util.日志记录
* Log4j
* SLF4J/日志

### <a name="metrics"></a>指标

* Micrometer
* JMX 指标

## <a name="sending-custom-telemetry-from-your-application"></a>从应用程序发送自定义遥测

3.0+ 的目标是允许您使用标准 API 发送自定义遥测数据。

我们支持微数、开放遥测 API 和流行的日志记录框架。 应用程序见解 Java 3.0 将自动捕获遥测数据，并将其与所有自动收集的遥测数据相关联。

因此，我们不打算此时发布具有应用程序见解 3.0 的 SDK。

应用程序见解 Java 3.0 已经在侦听发送到应用程序见解 Java SDK 2.x 的遥测数据。 此功能是现有 2.x 用户升级故事的重要组成部分，它填补了我们自定义遥测支持中的一个重要空白，直到 Open遥测 API 成为 GA。

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>使用应用程序见解 Java SDK 2.x 发送自定义遥测

添加到`applicationinsights-core-2.6.0.jar`应用程序（应用程序见解 Java 3.0 支持所有 2.x 版本，但如果您有选择，则值得使用最新版本）：

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

创建遥测客户端：

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

并将其用于发送自定义遥测。

### <a name="events"></a>事件

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>指标

您可以通过[缩数计](https://micrometer.io)发送指标遥测 ：

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

或者您也可以使用应用程序见解 Java SDK 2.x：

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
您可以通过您最喜爱的日志记录框架发送自定义日志遥测。

或者您也可以使用应用程序见解 Java SDK 2.x：

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>例外
您可以通过您最喜爱的日志记录框架发送自定义异常遥测。

或者您也可以使用应用程序见解 Java SDK 2.x：

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>从应用程序洞察 Java SDK 2.x 升级

如果您已经在应用程序中使用应用程序见解 Java SDK 2.x，则无需将其删除。 Java 3.0 代理将检测它，并捕获并关联您通过 Java SDK 2.x 发送的任何自定义遥测数据，同时禁止 Java SDK 2.x 执行的任何自动集合以防止重复捕获。

> [!NOTE]
> 注意：使用 3.0 代理时，将不会运行 Java SDK 2.x 遥测初始化器和遥测处理器。
