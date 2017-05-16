---
title: "在 Azure Application Insights 中监视 Docker 应用程序 | Microsoft Docs"
description: "Docker 性能计数器、事件和异常可连同容器化应用发送的遥测数据一起显示在 Application Insights 中。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: b888f3e7fd56aaf8104c1b76264a3753ca37f7c9
ms.contentlocale: zh-cn
ms.lasthandoff: 03/16/2017


---
# <a name="monitor-docker-applications-in-application-insights"></a>在 Application Insights 中监视 Docker 应用程序
[Docker](https://www.docker.com/) 容器中的生命周期事件和性能计数器可以在 Application Insights 上绘制成图表。 在主机的容器中安装 [Application Insights](app-insights-overview.md) 映像，该映像将显示主机及其他映像的性能计数器。

借助 Docker，可连同所有依赖项在轻量级容器中分配应用。 它们将在运行 Docker 引擎的任何主机计算机上运行。

在 Docker 主机上运行 [Application Insights 映像](https://hub.docker.com/r/microsoft/applicationinsights/)时，可获得以下优势：

* 获取主机上运行的所有容器的相关生命周期遥测数据 - 启动、停止等。
* 获取所有容器的性能计数器。 CPU、内存、网络使用量等。
* 如果针对容器中运行的应用程序[安装了 Application Insights SDK](app-insights-java-live.md)，这些应用程序的所有遥测数据可用于识别容器和主机计算机的其他属性。 例如，如果在多台主机上运行某应用的多个实例，则可按主机轻松筛选应用遥测数据。

![示例](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>设置 Application Insights 资源
1. 登录到 [Microsoft Azure 门户](https://azure.com)并打开应用的 Application Insights 资源；或[创建新资源](app-insights-create-new-resource.md)。 
   
    *应该使用哪种资源？* 如果主机上运行的应用由其他人开发，则需要[创建新的 Application Insights 资源](app-insights-create-new-resource.md)。 这是查看和分析遥测数据的位置。 （选择“常规”作为应用类型。）
   
    但如果你是应用的开发人员，我们建议[将 Application Insights SDK 添加到](app-insights-java-live.md)每个应用。 如果这些应用实际上都是单个业务应用程序的组件，则可以将所有应用配置为向一个资源发送遥测数据，并使用相一资源显示 Docker 生命周期和性能数据。 
   
    第三种方案是你开发了大多数应用，但想要使用不同的资源来显示其遥测数据。 此情况下，可能还需要为 Docker 数据创建不同的资源。 
2. 添加“Docker”磁贴：选择“添加磁贴”，从库中拖出“Docker”磁贴，然后单击“完成”。 
   
    ![示例](./media/app-insights-docker/03.png)
3. 单击“概要”下拉列表，然后复制检测密钥。 使用此密钥告知 SDK 要将遥测数据发送到哪个位置。

    ![示例](./media/app-insights-docker/02-props.png)

保持打开浏览器窗口，因为稍后要返回查看遥测数据。

## <a name="run-the-application-insights-monitor-on-your-host"></a>在主机上运行 Application Insights 监视器
设置一个显示遥测数据的位置后，可以设置用于收集和发送遥测数据的容器化应用。

1. 连接到 Docker 主机。 
2. 在以下命令中编辑检测密钥，然后运行该命令：
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

每台 Docker 主机只需要一个 Application Insights 映像。 如果应用程序部署在多台 Docker 主机上，请在每台主机上重复运行该命令。

## <a name="update-your-app"></a>更新应用程序
如果使用[用于 Java 的 Application Insights SDK](app-insights-java-get-started.md) 来检测应用程序，请将以下行添加到项目的 ApplicationInsights.xml 文件中的 `<TelemetryInitializers>` 元素下面：

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

这会将容器和主机 ID 等 Docker 信息添加到应用发送的每个遥测项中。

## <a name="view-your-telemetry"></a>查看遥测数据
返回到 Azure 门户中的 Application Insights 资源。

单击“Docker”磁贴。

片刻之后，就能看到来自 Docker 应用的数据，尤其是 Docker 引擎上有其他容器运行时。

下面是可能显示的一些视图。

### <a name="perf-counters-by-host-activity-by-image"></a>按主机列出的性能计数器、按映像列出的活动
![示例](./media/app-insights-docker/10.png)

![示例](./media/app-insights-docker/11.png)

单击任一主机或映像名称可获取更多详细信息。

若要自定义视图，请单击任一图表、网格标题，或使用“添加图表”。 

[详细了解指标资源管理器](app-insights-metrics-explorer.md)。

### <a name="docker-container-events"></a>Docker 容器事件
![示例](./media/app-insights-docker/13.png)

若要调查单个事件，请单击“搜索”。[](app-insights-diagnostic-search.md) 通过搜索和筛选找到所需的事件。 单击任一事件可查看详细信息。

### <a name="exceptions-by-container-name"></a>按容器名称列出的异常
![示例](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>已添加到应用遥测的 Docker 上下文
从使用 AI SDK 检测的应用程序发送的请求遥测数据，已使用 Docker 上下文进行补充：

![示例](./media/app-insights-docker/16.png)

处理器时间和可用内存性能计数器，已使用 Docker 容器名称进行补充和分组：

![示例](./media/app-insights-docker/15.png)

## <a name="q--a"></a>问题解答
*Application Insights 提供 Docker 所不能提供的哪些功能？*

* 按容器和映像提供性能计数器详细剖析。
* 将容器和应用数据集成在一个仪表板中。
* [导出遥测数据](app-insights-export-telemetry.md)，对数据库、Power BI 或其他仪表板做进一步的分析。

*如何从应用本身获取遥测数据？*

* 在应用中安装 Application Insights SDK。 了解不同应用类型中的操作方法：[Java Web 应用](app-insights-java-get-started.md)、[Windows Web 应用](app-insights-asp-net.md)。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>后续步骤

* [Application Insights for Java](app-insights-java-get-started.md)
* [Application Insights for Node.js](app-insights-nodejs.md)
* [Application Insights for ASP.NET](app-insights-asp-net.md)

