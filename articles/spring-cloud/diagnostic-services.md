---
title: 在 Azure 春季云中分析日志和指标 |Microsoft Docs
description: 了解如何分析 Azure 春季云中的诊断数据
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607855"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>通过诊断设置分析日志和指标

使用 Azure 春季 Cloud 的诊断功能，可以使用以下任何服务分析日志和指标：

* 使用 Azure Log Analytics，其中数据立即写入，无需先将数据写入存储。
* 将它们保存到存储帐户进行审核或手动检查。 您可以指定保留时间（天）。
* 将它们流式传输到事件中心，以供第三方服务或自定义分析解决方案引入。

若要开始，请启用其中一项服务来接收数据。 若要了解如何配置 Log Analytics，请参阅[Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)。 

## <a name="configure-diagnostics-settings"></a>配置诊断设置

1. 在 Azure 门户中，请切换到你的 Azure 春季云实例。
1. 选择 "**诊断设置**" 选项，然后选择 "**添加诊断设置**"。
1. 输入设置的名称，然后选择要将日志发送到的位置。 您可以选择以下三个选项的任意组合：
    * **存档到存储帐户**
    * **流式传输到事件中心**
    * **发送到 Log Analytics**

1. 选择要监视的日志类别和指标类别，然后指定保留时间（以天为单位）。 保留时间仅适用于存储帐户。
1. 选择“保存”。

> [!NOTE]
> 发出日志或指标后，或在存储帐户、事件中心或 Log Analytics 中出现日志或指标时，可能会出现长达15分钟的间隔。

## <a name="view-the-logs"></a>查看日志

### <a name="use-log-analytics"></a>使用 Log Analytics

1. 在 Azure 门户的左窗格中，选择 " **Log Analytics**"。
1. 选择添加诊断设置时选择的 "Log Analytics" 工作区。
1. 若要打开**日志搜索**窗格，请选择 "**日志**"。
1. 在 "**日志**搜索" 框中，输入简单查询，如下所示：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. 若要查看搜索结果，请选择 "**运行**"。
1. 可以通过设置筛选条件来搜索特定应用程序或实例的日志：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

若要详细了解 Log Analytics 中使用的查询语言，请参阅[Azure Monitor 日志查询](../azure-monitor/log-query/query-language.md)。

### <a name="use-your-storage-account"></a>使用存储帐户 

1. 在 Azure 门户的左窗格中，选择 "**存储帐户**"。

1. 选择添加诊断设置时选择的存储帐户。
1. 若要打开**Blob 容器**窗格，请选择**blob**。
1. 若要查看应用程序日志，请搜索名为 " **applicationconsole**" 的容器。
1. 若要查看应用程序指标，请搜索名为 " **pt1m**" 的容器。

若要了解有关将诊断信息发送到存储帐户的详细信息，请参阅[在 Azure 存储中存储和查看诊断数据](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)。

### <a name="use-your-event-hub"></a>使用事件中心

1. 在 Azure 门户的左窗格中，选择 "**事件中心**"。

1. 搜索并选择添加诊断设置时选择的事件中心。
1. 若要打开 "**事件中心列表**" 窗格，请选择 "**事件中心**"。
1. 若要查看应用程序日志，请搜索名为 " **applicationconsole**" 的事件中心。
1. 若要查看应用程序指标，请搜索名为 " **pt1m**" 的事件中心。

若要了解有关将诊断信息发送到事件中心的详细信息，请参阅[使用事件中心流式传输热路径中的数据 Azure 诊断](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)。

## <a name="analyze-the-logs"></a>分析日志

Azure Log Analytics 提供 Kusto，以便可以查询日志以进行分析。 若要快速了解如何使用 Kusto 查询日志，请查看[Log Analytics 教程](../azure-monitor/log-query/get-started-portal.md)。

应用程序日志提供有关应用程序的运行状况、性能等的关键信息。 在下一部分中，可以借助一些简单的查询来了解应用程序的当前状态和过去状态。

### <a name="show-application-logs-from-azure-spring-cloud"></a>显示 Azure 春季云中的应用程序日志

若要查看 Azure 春季云中的应用程序日志列表，按时间排序，并首先显示最新日志，请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>显示包含错误或异常的日志条目

若要查看提到错误或异常的未排序日志条目，请运行以下查询：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查询可查找错误，或修改查询字词以查找特定的错误代码或异常。 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>显示您的应用程序在过去一小时内报告的错误和异常的数目

若要创建显示您的应用程序在最近一小时内记录的错误数和异常数的饼图，请运行以下查询：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>了解有关查询应用程序日志的详细信息

Azure Monitor 通过使用 Log Analytics 为查询应用程序日志提供广泛支持。 若要了解有关此服务的详细信息，请参阅[Azure Monitor 中的日志查询入门](../azure-monitor/log-query/get-started-queries.md)。 有关生成查询以分析应用程序日志的详细信息，请参阅[Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。
