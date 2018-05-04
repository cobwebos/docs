---
title: 适用于活动 Java Web 应用的 Application Insights
description: 开始监视已在服务器上运行的 Web 应用程序
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: b8ddd43fb19b3800840180c458268301b5ec26f6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>适用于活动 Java Web 应用的 Application Insights


如果有一个 Web 应用程序在 J2EE 服务器上运行，无需更改代码或重新编译项目，就能开始使用 [Application Insights](app-insights-overview.md) 来监视该应用程序。 使用此选项可以获取发送到服务器的 HTTP 请求、未经处理的异常和性能计数器的相关信息。

需要订阅 [Microsoft Azure](https://azure.com)。

> [!NOTE]
> 此页上的过程在运行时会将 SDK 添加到 Web 应用。 如果不想要更新或重建源代码，此运行时检测非常有用。 但是，我们建议尽量改为[将 SDK 添加到源代码](app-insights-java-get-started.md)。 这样可以提供更多选项，包括编写代码来跟踪用户活动。
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1.获取 Application Insights 检测密钥
1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)
2. 创建新的 Application Insights 资源并将应用程序类型设置为 Java Web 应用程序。
   
    ![填写名称，选择 Java Web 应用，并单击“创建”](./media/app-insights-java-live/02-create.png)

    仅需数秒便可创建资源。

4. 打开新资源并获取其检测密钥。 稍后需要将此密钥粘贴到代码项目中。
   
    ![在新资源概述中，单击“属性”，并复制检测密钥](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2.下载该 SDK
1. 下载 [用于 Java 的 Application Insights SDK](https://aka.ms/aijavasdk)。 
2. 在服务器上，将 SDK 内容解压缩到从中加载项目二进制文件的目录。 如果使用 Tomcat，此目录通常在 `webapps/<your_app_name>/WEB-INF/lib` 下面

请注意，需要针对每个服务器实例和每个应用重复此过程。

## <a name="3-add-an-application-insights-xml-file"></a>3.添加 Application Insights xml 文件
可以在包含 SDK 的文件夹中创建 ApplicationInsights.xml。 将以下 XML 放入该文件。

替换为从 Azure 门户获取的检测密钥。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* 检测密钥随遥测的每个项一起发送，并告知 Application Insights 在资源中显示它。
* HTTP 请求组件是可选的。 它自动将请求和响应时间的遥测数据发送到门户。
* 事件关联是对 HTTP 请求组件的补充。 它将标识符分配到服务器收到的每个请求，并将此标识符添加为遥测的每个项的“Operation.Id”属性。 使用它可以通过在[诊断搜索](app-insights-diagnostic-search.md)中设置筛选器，来关联与每个请求关联的遥测。

## <a name="4-add-an-http-filter"></a>4.添加 HTTP 筛选器
在项目中找到并打开 web.xml 文件，然后将以下代码片段合并到 Web 应用节点下，即应用程序筛选器的配置位置。

为获得最准确的结果，应该在其他所有筛选器的前面映射该筛选器。

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5.检查防火墙例外
可能需要[设置例外才能发送传出数据](app-insights-ip-addresses.md)。

## <a name="6-restart-your-web-app"></a>6.重新启动 Web 应用
## <a name="7-view-your-telemetry-in-application-insights"></a>7.在 Application Insights 中查看遥测数据
返回 [Microsoft Azure 门户](https://portal.azure.com)中的 Application Insights 资源。

概述边栏选项卡显示了有关 HTTP 请求的遥测数据。 （如果未显示，请稍候片刻，并单击“刷新”。）

![示例数据](./media/app-insights-java-live/5-results.png)

单击任何图表以查看更详细的指标。 

![](./media/app-insights-java-live/6-barchart.png)

查看请求的属性时，可以查看与它关联的遥测事件，例如请求和异常。

![](./media/app-insights-java-live/7-instance.png)

[了解有关指标的详细信息。](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>后续步骤
* [将遥测添加到网页](app-insights-javascript.md)，用于监视页面视图和用户指标。
* [设置 Web 测试](app-insights-monitor-web-app-availability.md)，确保应用程序处于活动状态且能够做出响应。
* [捕获日志跟踪](app-insights-java-trace-logs.md)
* [搜索事件和日志](app-insights-diagnostic-search.md)帮助诊断问题。

