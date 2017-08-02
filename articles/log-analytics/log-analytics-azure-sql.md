---
title: "Log Analytics 中的 Azure SQL Analytics 解决方案 | Microsoft 文档"
description: "Azure SQL Analytics 解决方案可帮助用户管理 Azure SQL 数据库。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 165568731debf2cd81a88170833f95ca2e7080e5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>在 Log Analytics 中使用 Azure SQL Analytics（预览版）监视 Azure SQL 数据库

![Azure SQL Analytics 符号](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Azure Log Analytics 中的 Azure SQL Analytics 解决方案收集和可视化重要的 Azure SQL 性能指标。 使用解决方案收集指标后，即可利用这些指标创建自定义监视规则和警报。 此外，还可以跨多个 Azure 订阅和弹性池监视 Azure SQL 数据库和弹性池指标并将其可视化。 该解决方案还可以帮助用户确定应用程序堆栈的每个层的问题。  它使用 [Azure 诊断指标](log-analytics-azure-storage.md)和 Log Analytics 视图，在单个 Log Analytics 工作区中呈现有关所有 Azure SQL 数据库和弹性池的数据。

此预览版解决方案目前支持每个工作区使用多达 150,000 个 Azure SQL 数据库和 5,000 个 SQL 弹性池。

与其他适用于 Log Analytics 的解决方案一样，Azure SQL Analytics 解决方案可以帮助用户监视和接收有关 Azure 资源（在此示例中为 Azure SQL 数据库）运行状况的通知。 Microsoft Azure SQL 数据库是可缩放的关系数据库服务，为运行在 Azure 云中的应用程序提供熟悉的类似于 SQL Server 的功能。 Log Analytics 可帮助用户收集、关联和可视化结构化和非结构化数据。

## <a name="connected-sources"></a>连接的源

Azure SQL Analytics 解决方案不使用代理连接 Log Analytics 服务。

下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| [Windows 代理](log-analytics-windows-agents.md) | 否 | 该解决方案不使用直接 Windows 代理。 |
| [Linux 代理](log-analytics-linux-agents.md) | 否 | 该解决方案不使用直接 Linux 代理。 |
| [SCOM 管理组](log-analytics-om-agents.md) | 否 | 该解决方案不使用从 SCOM 代理到 Log Analytics 的直接连接。 |
| [Azure 存储帐户](log-analytics-azure-storage.md) | 否 | Log Analytics 不会从存储帐户中读取数据。 |
| [Azure 诊断](log-analytics-azure-storage.md) | 是 | Azure 指标数据由 Azure 直接发送到 Log Analytics。 |

## <a name="prerequisites"></a>先决条件

- 一个 Azure 订阅。 如果没有该订阅，可以[免费](https://azure.microsoft.com/free/)创建一个。
- Log Analytics 工作区。 在开始使用此解决方案之前，用户可以使用现有的工作区，也可以[创建一个新的](log-analytics-get-started.md)。
- 为 Azure SQL 数据库和弹性池启用 Azure 诊断，并[将其配置为发送数据到 Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)。

## <a name="configuration"></a>配置

执行以下步骤，将 Azure SQL Analytics 解决方案添加到你的工作区。

1. 从 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) 或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 Azure SQL Analytics 解决方案添加到工作区。
2. 在 Azure 门户中，单击“新建”（+ 符号），然后在资源列表中，选择“监视 + 管理”。  
    ![监视 + 管理](./media/log-analytics-azure-sql/monitoring-management.png)
3. 在“监视 + 管理”列表中，单击“全部查看”。
4. 在“推荐”列表中，单击“更多”，然后在新列表中找到并选中“Azure SQL Analytics (预览版)”。  
    ![Azure SQL Analytics 解决方案](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. 在“Azure SQL Analytics (预览版)”边栏选项卡中，单击“创建”。  
    ![创建](./media/log-analytics-azure-sql/portal-create.png)
6. 在“创建新的解决方案”边栏选项卡中，选择要向其添加解决方案的工作区，然后单击“创建”。  
    ![添加到工作区](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>配置多个 Azure 订阅

若要支持多个订阅，请使用 [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)（通过 PowerShell 启用 Azure 资源指标日志记录）中的 PowerShell 脚本。 在执行脚本时提供工作区资源 ID 作为参数，以便将诊断数据从一个 Azure 订阅中的资源发送到另一 Azure 订阅中的工作区。

**示例**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>使用解决方案

将解决方案添加到工作区时，“Azure SQL Analytics”磁贴也会添加到工作区并显示在“概览”中。 该磁贴显示解决方案连接到的 Azure SQL 数据库和 Azure SQL 弹性池的数目。

![“Azure SQL Analytics”磁贴](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>查看 Azure SQL Analytics 数据

单击“Azure SQL Analytics”磁贴打开“Azure SQL Analytics”仪表板。 该仪表板包含下面定义的边栏选项卡。 每个边栏选项卡列出最多 15 个资源（订阅、服务器、弹性池和数据库）。 单击任何资源可打开该特定资源的仪表板。 弹性池或数据库包含图表，其中显示了所选资源的指标。 单击某个图表可打开“日志搜索”对话框。

| 边栏选项卡 | 说明 |
|---|---|
| 订阅 | 订阅列表，以及已连接的服务器、池和数据库的数目。 |
| 服务器 | 服务器列表，以及已连接的池和数据库的数目。 |
| 弹性池 | 已连接的弹性池的列表，以及观察期间内的最大 GB 和 eDTU。 |
|数据库 | 已连接的数据库的列表，以及观察期间内的最大 GB 和 DTU。|


### <a name="analyze-data-and-create-alerts"></a>分析数据和创建警报

可以使用来自 Azure SQL 数据库资源的数据轻松创建警报。 以下是几个有用的可用于警报的[日志搜索](log-analytics-log-searches.md)查询：

*Azure SQL 数据库上的高 DTU*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/DATABASES/"* MetricName=dtu_consumption_percent | measure Avg(Average) by Resource interval 5minutes
```

*Azure SQL 数据库弹性池上的高 DTU*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource interval 5minutes
```

可以使用这些基于警报的查询，针对 Azure SQL 数据库和弹性池的特定阈值设置警报。 若要配置 OMS 工作区的警报，请执行以下操作：

#### <a name="to-configure-an-alert-for-your-workspace"></a>配置工作区警报

1. 转到 [OMS 门户](http://mms.microsoft.com/)并登录。
2. 打开为解决方案配置的工作区。
3. 在“概览”页上，单击“Azure SQL Analytics (预览版)”磁贴。
4. 运行示例查询之一。
5. 在“日志搜索”中，单击“警报”。  
![在搜索中创建警报](./media/log-analytics-azure-sql/create-alert01.png)
6. 在“添加警报规则”页上，根据需要配置适当的属性和特定的阈值，然后单击“保存”。  
![添加警报规则](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-analytics-data"></a>操作 Azure SQL Analytics 数据

举例来说，用户可以执行的最有用查询之一是跨所有 Azure 订阅比较所有 Azure SQL 弹性池的 DTU 使用率。 数据库吞吐量单位 (DTU) 用于描述性能级别为“基础”、“标准”和“高级”的数据库和池的相对容量。 DTU 取决于对 CPU、内存、读取和写入的混合度量。 DTU 增加时，性能级别的性能也增加。 例如，就性能来说，DTU 为 5 时的性能级别是 DTU 为 1 时的性能级别的 5 倍。 最大 DTU 配额适用于每个服务器和弹性池。

运行以下日志搜索查询即可轻松判断 Azure SQL 弹性池的使用是不足还是过度。

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

在以下示例中，可以看到一个弹性池的 DTU 使用率很高，接近 100%，而其他弹性池的使用率很低。 用户可以使用 Azure 活动日志进一步实施调查，对环境中可能是最近所做的更改进行故障排除。

![日志搜索结果 - 高使用率](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>另请参阅

- 使用 Log Analytics 中的[日志搜索](log-analytics-log-searches.md)查看 Azure SQL 的详细数据。
- [创建你自己的仪表板](log-analytics-dashboards.md)，显示 Azure SQL 数据。
- 发生特定 Azure SQL 事件时[创建警报](log-analytics-alerts.md)。

