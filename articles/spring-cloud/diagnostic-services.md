---
title: 分析 Azure 春云中的日志和指标 |微软文档
description: 了解如何分析 Azure 春云中的诊断数据
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: adbcf28cfbbe2ea3b7cc9c7fd0d1c76246938344
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870407"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>使用诊断设置分析日志和指标

使用 Azure 春云的诊断功能，可以使用以下任一服务分析日志和指标：

* 使用 Azure 日志分析，其中将数据写入 Azure 存储。 将日志导出到日志分析时，会有延迟。
* 将日志保存到存储帐户以进行审核或手动检查。 您可以指定保留时间（以天表示）。
* 将日志流式传输到事件中心，以便由第三方服务或自定义分析解决方案引入。

选择要监视的日志类别和指标类别。

> [!TIP]
> 只想流式传输日志？ 请查看此[Azure CLI 命令](https://docs.microsoft.com/cli/azure/ext/spring-cloud/spring-cloud/app?view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs)！

## <a name="logs"></a>日志

|日志 | 说明 |
|----|----|
| **ApplicationConsole** | 所有客户应用程序的控制台日志。 | 
| **系统日志** | 目前，只有[春云配置服务器](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server)在此类别中日志。 |

## <a name="metrics"></a>指标

有关指标的完整列表，请参阅 Spring [Cloud 指标](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)。

要开始，使这些服务之一能够接收数据。 要了解如何配置日志分析，请参阅在[Azure 监视器 中开始使用日志分析](../azure-monitor/log-query/get-started-portal.md)。 

## <a name="configure-diagnostics-settings"></a>配置诊断设置

1. 在 Azure 门户中，转到 Azure 春云实例。
1. 选择 **"诊断设置"** 选项，然后选择 **"添加诊断"设置**。
1. 输入设置的名称，然后选择要发送日志的位置。 您可以选择以下三个选项的任意组合：
    * **存档到存储帐户**
    * **流式传输到事件中心**
    * **发送到 Log Analytics**

1. 选择要监视的日志类别和指标类别，然后指定保留时间（以天表示）。 保留时间仅适用于存储帐户。
1. 选择“保存”。 

> [!NOTE]
> 在发出日志或指标以及日志或指标显示在存储帐户、事件中心或日志分析中之间，间隔可能长达 15 分钟。

## <a name="view-the-logs-and-metrics"></a>查看日志和指标
查看日志和指标的方法有多种，如下标题所述。

### <a name="use-logs-blade"></a>使用日志边栏选项卡

1. 在 Azure 门户中，转到 Azure 春云实例。
1. 要打开 **"日志搜索"** 窗格，请选择 **"日志**"。
1. 在 **"日志**"搜索框中
   * 要查看日志，请输入一个简单的查询，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * 要查看指标，请输入一个简单的查询，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```
1. 要查看搜索结果，请选择"**运行**"。

### <a name="use-log-analytics"></a>使用 Log Analytics

1. 在 Azure 门户中，在左侧窗格中，选择 **"日志分析**"。
1. 选择添加诊断设置时选择的日志分析工作区。
1. 要打开 **"日志搜索"** 窗格，请选择 **"日志**"。
1. 在 **"日志**"搜索框中，
   * 要查看日志，请输入一个简单的查询，例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * 要查看指标，请输入一个简单的查询，例如：

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 要查看搜索结果，请选择"**运行**"。
1. 您可以通过设置筛选器条件来搜索特定应用程序或实例的日志：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`区分大小写，但不`=~`敏感。

要了解有关日志分析中使用的查询语言的更多信息，请参阅[Azure 监视器日志查询](../azure-monitor/log-query/query-language.md)。

### <a name="use-your-storage-account"></a>使用存储帐户 

1. 在 Azure 门户中，在左侧窗格中，选择 **"存储帐户**"。

1. 选择添加诊断设置时选择的存储帐户。
1. 要打开**Blob 容器**窗格，请选择**Blob**。
1. 要查看应用程序日志，请搜索一个称为**见解日志-应用程序控制台的**容器。
1. 要查看应用程序指标，请搜索一个称为**见解度量-pt1m 的**容器。

要了解有关向存储帐户发送诊断信息的详细信息，请参阅在[Azure 存储 中存储和查看诊断数据](../storage/common/storage-introduction.md)。

### <a name="use-your-event-hub"></a>使用活动中心

1. 在 Azure 门户中，在左侧窗格中，选择**事件中心**。

1. 搜索并选择添加诊断设置时选择的事件中心。
1. 要打开**事件中心列表**窗格，请选择**事件中心**。
1. 要查看应用程序日志，请搜索一个称为**见解日志-应用程序控制台**的事件中心。
1. 要查看应用程序指标，请搜索一个事件中心，称为**见解度量-pt1m**。

要了解有关向事件中心发送诊断信息的详细信息，请参阅[使用事件中心在热路径中流式传输 Azure 诊断数据](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)。

## <a name="analyze-the-logs"></a>分析日志

Azure 日志分析正在使用 Kusto 引擎运行，因此您可以查询日志进行分析。 有关使用 Kusto 查询日志的快速介绍，请查看[日志分析教程](../azure-monitor/log-query/get-started-portal.md)。

应用程序日志提供有关应用程序运行状况、性能等的关键信息和详细日志。 在下一节中，有一些简单的查询可帮助您了解应用程序的当前和过去状态。

### <a name="show-application-logs-from-azure-spring-cloud"></a>从 Azure 春云显示应用程序日志

要查看 Azure Spring Cloud 的应用程序日志列表（按时间排序，首先显示最新日志）请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>显示包含错误或异常的日志条目

要查看提及错误或异常的未排序的日志条目，请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查询可以查找错误，或修改查询术语以查找特定的错误代码或异常。 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>显示应用程序在过去一小时内报告的错误和异常数

要创建显示应用程序在最后一小时记录的错误和异常数的饼图，请运行以下查询：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>了解有关查询应用程序日志的更多信息

Azure 监视器通过使用日志分析为查询应用程序日志提供了广泛的支持。 要了解有关此服务详细信息，请参阅在[Azure 监视器 中开始使用日志查询](../azure-monitor/log-query/get-started-queries.md)。 有关构建查询以分析应用程序日志的详细信息，请参阅[Azure 监视器 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。
