---
title: 在 Azure 春季云中分析日志和指标 |Microsoft Docs
description: 了解如何分析 Azure 春季云中的诊断数据
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038881"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>通过诊断设置分析日志和指标

使用 Azure 春季 Cloud 的诊断功能，可以使用以下服务之一分析日志和指标：

* 用 Azure Log Analytics 分析这些数据，其中数据立即写入 Azure Log Analytics，无需先将数据写入存储。
* 将它们保存到存储帐户进行审核或手动检查。 您可以指定保留时间（天）。
* 将它们流式传输到事件中心，以供第三方服务或自定义分析解决方案引入。

若要开始，你需要启用其中一项服务来接收数据。  若要了解有关配置 Log Analytics 的信息，请查看[本教程](../azure-monitor/log-query/get-started-portal.md)。  

## <a name="configure-diagnostic-settings"></a>配置诊断设置

1. 在 Azure 门户中，请参阅 Azure 春季云实例。
1. 选择 "**诊断设置**" 菜单选项。
1. 选择 "**添加诊断设置**" 按钮。
1. 输入设置的名称，并选择要将日志发送到的位置。 您可以选择以下三个选项的任意组合：
    * 存档到存储帐户
    * 流式传输到事件中心
    * 发送到 Log Analytics

1. 选择要监视的日志类别和指标类别，并指定保留时间（以天为单位）。 保留时间仅适用于存储帐户。
1. 选择“保存”以应用设置。

> [!NOTE]
> 发出日志或指标时，可能最多有15分钟的时间，它们显示在存储帐户/事件中心/Log Analytics 之间。

## <a name="viewing-logs"></a>查看日志

### <a name="using-log-analytics"></a>使用 Log Analytics

1. 从 "Azure 门户" 中，从左侧导航菜单中选择 "Log Analytics"。
1. 选择添加诊断设置时选择的 "Log Analytics" 工作区。
1. 选择 `Logs` 打开 "日志搜索" 边栏选项卡。
1. 在 "日志搜索" 框中输入简单查询。  例如：

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. 选择 `Run` 以查看搜索结果。
1. 可以通过设置筛选条件来搜索特定应用程序或实例的日志：

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

详细了解[本文 Log Analytics 中](../azure-monitor/log-query/query-language.md)使用的查询语言

### <a name="using-logs-and-metrics-in-storage-account"></a>使用存储帐户中的日志和指标

1. 从 Azure 门户的左侧导航菜单中，选择 "存储帐户"。
1. 选择添加诊断设置时选择的存储帐户。
1. 选择 `Blobs` 条目以打开 "Blob 容器" 边栏选项卡。
1. 查找名为 `insights-logs-applicationconsole` 的容器，以查看应用程序日志。
1. 查找名为 `insights-metrics-pt1m` 的容器，以查看应用程序指标。

[了解有关将诊断信息发送到存储帐户的详细信息。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>使用事件中心

1. 从 Azure 门户的左侧导航菜单中，选择 "事件中心"。
1. 找到并选择添加诊断设置时选择的事件中心。
1. 选择 `Event Hubs` 打开 "事件中心列表" 边栏选项卡。
1. 查找名为 `insights-logs-applicationconsole` 的事件中心，以查看应用程序日志。
1. 查找名为 `insights-metrics-pt1m` 的事件中心，以查看应用程序指标。

[了解有关将诊断信息发送到事件中心的详细信息。](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>分析日志

Azure Log Analytics 提供 Kusto，因此你可以查询日志以进行分析。  查看[Log Analytics 教程](../azure-monitor/log-query/get-started-portal.md)，了解如何使用 Kusto 查询日志。

应用程序日志提供有关应用程序的运行状况、性能等的关键信息。  下面是一些简单的查询，可帮助你了解应用程序的当前状态和过去状态。

### <a name="show-application-logs-from-azure-spring-cloud"></a>显示 Azure 春季云中的应用程序日志

若要查看 Azure 春季云中的应用程序日志列表，请按时间排序，首先显示最新的日志：

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>显示包含错误或异常的日志条目

此查询允许您查看提及错误或异常的日志条目。  不会对结果进行排序。

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

使用此查询可查找错误，或修改查询字词以查找特定的错误代码或异常。  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>显示您的应用程序在过去一小时内报告的错误和异常的数目

此查询创建一个饼图，其中显示了你的应用程序在最后一个小时内记录的错误和异常的数目：

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>了解有关查询应用程序日志的详细信息

Azure Monitor 为使用 Log Analytics 查询应用程序日志提供了广泛的支持。  若要了解有关此服务的详细信息，请参阅有关使用 Azure Monitor 的[日志查询](../azure-monitor/log-query/get-started-queries.md)的教程。 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)提供了有关生成查询以分析应用程序日志的详细信息。
