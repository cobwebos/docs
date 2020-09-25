---
title: 快速入门 - 通过日志、指标和跟踪来监视 Azure Spring Cloud 应用
description: 使用日志流式处理、日志分析、指标和跟踪来监视 Azure Spring Cloud 上的 Piggymetrics 示例应用。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 96a97b9b141d434f201da4c7e36f6715186a652e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903131"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>快速入门：通过日志、指标和跟踪来监视 Azure Spring Cloud 应用

::: zone pivot="programming-language-csharp"
使用 Azure Spring Cloud 中的内置监视功能，可以调试和监视复杂问题。 Azure Spring Cloud 将 Steeltoe [分布式跟踪](https://steeltoe.io/docs/3/tracing/distributed-tracing) 与 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 集成。 这种集成可以通过 Azure 门户提供强大的日志、指标和分布式跟踪功能。

以下过程说明如何通过你在前面快速入门中部署的示例应用使用日志流式处理、Log Analytics、指标和分布式跟踪。

## <a name="prerequisites"></a>先决条件

* 完成此系列中前面的快速入门：

  * [预配 Azure Spring Cloud 服务](spring-cloud-quickstart-provision-service-instance.md)。
  * [设置 Azure Spring Cloud 配置服务器](spring-cloud-quickstart-setup-config-server.md)。
  * [构建和部署应用](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>日志

可以通过两种方式在 Azure Spring Cloud 上查看日志：每个应用实例的实时日志的日志流式处理或具有高级查询功能的聚合日志的 Log Analytics 。

### <a name="log-streaming"></a>日志流式处理

可以通过以下命令在 Azure CLI 中使用日志流式处理。

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

将会看到类似于以下示例的输出：

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> 使用 `az spring-cloud app logs -h` 浏览更多参数和日志流功能。

### <a name="log-analytics"></a>Log Analytics

1. 转到“服务 | 概述”页，然后在“监视”部分中选择“日志”  。 选择 Azure Spring Cloud 的一个示例查询上的“运行”。

   [ ![Logs Analytics 条目](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. 编辑查询以删除将显示限制为警告和错误日志的 Where 子句。

1. 然后选择 `Run`，随即会显示日志。 有关编写查询的更多指导，请参阅 [Azure Logs Analytics 文档](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)。

   [ ![Logs Analytics 查询 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>指标

1. 在 Azure 门户中转到“服务 | 概述”页，然后在“监视”部分中选择“指标”  。 通过为“指标”选择 `system.cpu.usage` 并为“聚合”选择 `Avg` 来添加第一个指标，以查看总体 CPU 使用情况的时间线 。

   [ ![指标条目 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. 单击工具栏中的“添加筛选器”，选择 `App=solar-system-weather`，以仅查看“solar-system-weather”应用的 CPU 使用情况 。

   [ ![在指标中使用筛选器 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. 关闭在前一步中创建的筛选器，选择“应用拆分”，然后为“值”选择 `App` 以查看不同应用的 CPU 使用情况 。

   [ ![在指标中应用拆分 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>分布式跟踪

1. 在Azure 门户中转到“服务 | 概述”页，然后在“监视”部分中选择“分布式跟踪”  。 然后选择右侧的“查看应用程序映射”选项卡。

   [ ![分布式跟踪条目 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 现在可以查看应用间的调用状态。 

   [ ![分布式跟踪概述 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. 选择“solar-system-weather”和“planet-weather-provider”之间的链接，查看更多详细信息，如最慢的 HTTP 方法调用 。

   [ ![分布式跟踪 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. 最后，选择“调查性能”以探索更强大的内置性能分析。

   [ ![分布式跟踪性能 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
使用 Azure Spring Cloud 中的内置监视功能，可以调试和监视复杂问题。 Azure Spring Cloud 将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 集成。 这种集成可以通过 Azure 门户提供强大的日志、指标和分布式跟踪功能。 以下过程说明如何通过已部署的 PiggyMetrics 应用使用日志流式处理、Log Analytics、指标和分布式跟踪。

## <a name="prerequisites"></a>先决条件

完成前面的步骤： 

* [预配 Azure Spring Cloud 的实例](spring-cloud-quickstart-provision-service-instance.md)
* [设置 Config Server](spring-cloud-quickstart-setup-config-server.md)
* [构建和部署应用](spring-cloud-quickstart-deploy-apps.md)。

## <a name="logs"></a>日志

可以通过两种方式在 Azure Spring Cloud 上查看日志：每个应用实例的实时日志的日志流式处理或具有高级查询功能的聚合日志的 Log Analytics 。

### <a name="log-streaming"></a>日志流式处理

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

可以通过以下命令在 Azure CLI 中使用日志流式处理。

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

将显示如下所示的日志：

[ ![从 Azure CLI 进行日志流式传输](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> 使用 `az spring-cloud app logs -h` 浏览更多参数和日志流功能。

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

使用 Azure Toolkit for IntelliJ 获取日志：

1. 选择“Azure 资源管理器”，然后选择“Spring Cloud”。

1. 右键单击正在运行的应用。

1. 从下拉列表中选择“流式处理日志”。

   ![选择流式处理日志](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. 选择“实例”。

   ![选择实例](media/spring-cloud-intellij-howto/select-instance.png)
    
1. 流式处理日志将显示在输出窗口中。

   ![流式处理日志输出](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. 转到“服务 | 概述”页，然后在“监视”部分中选择“日志”  。 单击 Azure Spring Cloud 的一个示例查询上的“运行”。 

   [ ![Logs Analytics 条目](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. 然后，将显示已筛选的日志。 有关编写查询的更多指导，请参阅 [Azure Logs Analytics 文档](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)。

   [ ![Logs Analytics 查询](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>指标

1. 转到“服务 | 概述”页，然后在“监视”部分中选择“指标”  。 通过为“指标”选择 `system.cpu.usage` 并为“聚合”选择 `Avg` 来添加第一个指标，以查看总体 CPU 使用情况的时间线 。

   [ ![指标条目](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. 单击上面工具栏中的“添加筛选器”，选择 `App=Gateway`，以仅查看“网关”应用的 CPU 使用情况 。

   [ ![在指标中使用筛选器](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. 关闭上面创建的筛选器，单击“应用拆分”，然后为“值”选择 `App`，以查看不同应用的 CPU 使用情况 。

   [ ![在指标中应用拆分](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>分布式跟踪

1. 转到“服务 | 概述”页，然后在“监视”部分中选择“分布式跟踪”  。 然后单击右侧的“查看应用程序映射”选项卡。

   [ ![分布式跟踪条目](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 现在可以查看 Piggymetrics 应用间的调用状态。 

   [ ![分布式跟踪概述](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. 单击“网关”和“帐户-服务”之间的链接以查看更多详细信息，例如 HTTP 方法的最慢调用 。

   [ ![分布式跟踪](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. 最后，单击“调查性能”以探索更强大的内置性能分析。

   [ ![分布式跟踪性能](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>清理资源

在这些快速入门中，你创建了 Azure 资源，如果这些资源保留在订阅中，将继续产生费用。 如果你认为将来不需要这些资源，请使用门户或通过在 Cloud Shell 中运行以下命令删除资源组：

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

在前面的快速入门中，你还设置了默认资源组名称。 如果不打算继续学习下一个快速入门，请通过运行以下 CLI 命令清除该默认名称：

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>后续步骤

若要了解 Azure Spring Cloud 的更多监视功能，请参阅：

> [!div class="nextstepaction"]
> [诊断服务](diagnostic-services.md)
>
> [分布式跟踪](spring-cloud-tutorial-distributed-tracing.md)
>
> [实时流式传输日志](spring-cloud-howto-log-streaming.md)
