---
title: 在 Azure Application Insights 中监视 Java Web 应用的性能 | Microsoft Docs
description: 使用 Application Insights 监视 Java 网站的扩展性能和使用情况。
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 3a771da2a1ef0333d49e1d83530b3d3032a550d2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>监视 Java Web 应用中的依赖项、捕获的异常和方法执行时间


如果已[使用 Application Insights 检测了 Java Web 应用][java]，则无需更改任何代码，就能使用 Java 代理来获取更深入的见解：

* **依赖项：**有关应用程序对其他组件的调用的数据，包括：
  * 已捕获通过 HttpClient、OkHttp 和 RestTemplate (Spring) 发出的 REST 调用。
  * 已捕获通过 Jedis 客户端发出的 Redis 调用。
  * **[JDBC 调用](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - 会自动捕获 MySQL、SQL Server 和 Oracle DB 命令。 对于 MySQL，如果调用时间长于 10 秒，代理将报告查询计划。
* **捕获的异常：**代码处理的异常相关信息。
* **方法执行时间：**有关执行特定方法所花费的时间的信息。

若要使用 Java 代理，请在服务器上安装该代理。 必须使用 [Application Insights Java SDK][java] 检测 Web 应用。 

## <a name="install-the-application-insights-agent-for-java"></a>安装适用于 Java 的 Application Insights 代理
1. 在运行 Java 服务器的计算机上[下载该代理](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)。 请确保下载与 Application Insights Java SDK 核心和 Web 包版本相同的 Java 代理版本。
2. 编辑应用程序服务器启动脚本，并添加以下 JVM：
   
    `javaagent:`*代理 JAR 文件的完整路径*
   
    例如，在 Linux 计算机上的 Tomcat 中：
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 重新启动应用程序服务器。

## <a name="configure-the-agent"></a>配置代理
创建名为 `AI-Agent.xml` 的文件，并将它放在代理 JAR 文件所在的同一个文件夹中。

设置 xml 文件的内容。 编辑以下示例，根据需要包含或省略功能。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

必须针对各个方法启用报告异常和方法计时。

默认情况下，`reportExecutionTime` 为 true，`reportCaughtExceptions` 为 false。

## <a name="view-the-data"></a>查看数据
在 Application Insights 资源中，聚合的远程依赖项和方法执行时间显示在[“性能”磁贴下][metrics]。

若要搜索依赖项、异常和方法报告的单个实例，请打开[搜索][diagnostic]。

[诊断依赖项问题 - 了解详细信息](app-insights-asp-net-dependencies.md#diagnosis)。

## <a name="questions-problems"></a>有疑问？ 遇到问题？
* 没有数据？ [设置防火墙异常](app-insights-ip-addresses.md)
* [Java 故障排除](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
