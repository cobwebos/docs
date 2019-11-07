---
title: Azure Analysis Services 诊断日志记录 | Microsoft Docs
description: 描述如何设置 Azure 资源诊断日志记录以监视 Azure Analysis Services 服务器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b8ae2c529bebebae4ebc2d7b0b8a7e420fe9bcc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572775"
---
# <a name="setup-diagnostic-logging"></a>设置诊断日志记录

监视服务器性能对于任何 Analysis Services 解决方案都至关重要。 通过 [Azure 资源诊断日志记录](../azure-monitor/platform/resource-logs-overview.md)，可监视日志并将其发送到 [Azure 存储](https://azure.microsoft.com/services/storage/)，将其流式处理到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并将其导出到 [Azure Monitor 日志](../azure-monitor/azure-monitor-log-hub.md)。

![存储、事件中心或 Azure Monitor 日志的诊断日志记录](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>会记录哪些内容？

可选择“引擎”“服务”和“指标”类别。

### <a name="engine"></a>引擎

选择“引擎”将记录所有 [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)。 不能选择单个事件。 

|XEvent 类别 |事件名称  |
|---------|---------|
|安全审核    |   审核登录      |
|安全审核    |   审核注销      |
|安全审核    |   审核服务器启动和停止      |
|进度报告     |   进度报告开始      |
|进度报告     |   进度报告结束      |
|进度报告     |   进度报告进行中      |
|查询     |  查询开始       |
|查询     |   查询结束      |
|命令     |  命令开始       |
|命令     |  命令结束       |
|错误和警告     |   错误      |
|发现     |   发现结束      |
|通知     |    通知     |
|会话     |  会话初始化       |
|锁    |  死锁       |
|查询处理     |   VertiPaq SE 查询开始      |
|查询处理     |   VertiPaq SE 查询结束      |
|查询处理     |   VertiPaq SE 查询缓存匹配      |
|查询处理     |   直接查询开始      |
|查询处理     |  直接查询结束       |

### <a name="service"></a>服务

|操作名称  |发生条件  |
|---------|---------|
|ResumeServer     |    恢复服务器     |
|SuspendServer    |   暂停服务器      |
|DeleteServer     |    删除服务器     |
|RestartServer    |     用户通过 SSMS 或 PowerShell 重启服务器    |
|GetServerLogFiles    |    用户通过 PowerShell 导出服务器日志     |
|ExportModel     |   用户通过在 Visual Studio 中使用 Open 在门户中导出模型     |

### <a name="all-metrics"></a>所有指标

"指标" 类别将相同的[服务器指标](analysis-services-monitor.md#server-metrics)记录到 AzureMetrics 表中。 如果使用的是查询[横向扩展](analysis-services-scale-out.md)，并且需要为每个读取副本单独使用度量值，请改用 AzureDiagnostics 表，其中**OperationName**等于**LogMetric**。

## <a name="setup-diagnostics-logging"></a>设置诊断日志记录

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中找到服务器，单击左侧导航栏中的“诊断日志”，然后单击“启用诊断”。

    ![在 Azure 门户中启用 Azure Cosmos DB 的诊断日志记录](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. 在“诊断设置”中，指定以下选项： 

    * **名称**。 为要创建的日志输入名称。

    * **存档到存储帐户**。 要使用此选项，需要一个可连接到的现有存储帐户。 请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md)。 按照说明创建一个资源管理器常规用途帐户，然后返回到门户中的此页面来选择存储帐户。 新创建的存储帐户可能几分钟后才会显示在下拉菜单中。
    * **流式传输到事件中心**。 要使用此选项，需要一个可连接到的现有事件中心命名空间和事件中心。 若要了解详细信息，请参阅[使用 Azure 门户创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。 然后在门户中返回到此页，选择事件中心命名空间和策略名称。
    * 发送到 Azure Monitor（Log Analytics 工作区）。 若要使用此选项，可以使用现有工作区或者在门户中[新建工作区](../azure-monitor/learn/quick-create-workspace.md)资源。 有关查看日志的详细信息，请参阅本文中的[在 Log Analytics 工作区中查看日志](#view-logs-in-log-analytics-workspace)。

    * **引擎** 选择此选项以记录 Xevent。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后会自动删除日志。
    * **服务**。 选择此选项以记录服务级别事件。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后会自动删除日志。
    * **指标** 选择此选项可在[指标](analysis-services-monitor.md#server-metrics)中存储详细数据。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后会自动删除日志。

3. 单击“保存”。

    如果收到错误，指出“无法更新诊断 \<工作区名称> 的诊断。 订阅 \<订阅 ID> 未注册，无法使用 microsoft.insights。”， 请遵照[排查 Azure 诊断问题](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)中的说明注册帐户，然后重试此过程。

    若要更改在将来的任意时间点保存诊断日志的方式，可以随时返回此页以修改设置。

### <a name="powershell"></a>PowerShell

以下基本命令可以帮助你开始使用。 如需有关使用 PowerShell 为存储帐户设置日志记录的逐步指导，请参阅本文稍后部分的教程。

若要使用 PowerShell 启用指标和诊断日志记录，请使用以下命令：

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   存储帐户 ID 是需要将日志发送到的存储帐户的资源 ID。

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure 服务总线规则 ID 是以下格式的字符串：

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 若要允许将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 可以使用以下命令获取 Log Analytics 工作区的资源 ID：

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

可以组合这些参数以启用多个输出选项。

### <a name="rest-api"></a>REST API

了解如何[使用 Azure Monitor REST API 更改诊断设置](https://docs.microsoft.com/rest/api/monitor/)。 

### <a name="resource-manager-template"></a>资源管理器模板

了解如何[在创建资源时使用资源管理器模板启用诊断设置](../azure-monitor/platform/diagnostic-settings-template.md)。 

## <a name="manage-your-logs"></a>管理日志

通常在设置日志记录数小时后可使用日志。 存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。
* 请务必设置保留期，以便从存储帐户中删除旧日志。

## <a name="view-logs-in-log-analytics-workspace"></a>在 Log Analytics 工作区中查看日志

在 Log Analytics 工作区资源中，指标和服务器事件与 Xevent 集成，以便并列分析。 Log Analytics 工作区还可配置为接收来自其他 Azure 服务的事件，从而提供整个体系结构的诊断日志记录数据。

若要查看诊断数据，请从 Log Analytics 工作区的左侧菜单中打开“日志”。

![Azure 门户中的“日志搜索”选项](./media/analysis-services-logging/aas-logging-open-log-search.png)

在查询生成器中，展开 LogManagement**AzureDiagnostics** > 。 AzureDiagnostics 包括引擎和服务事件。 注意即时创建了一个查询。 EventClass\_s 字段包含 xEvent 名称，如果使用 Xevent 进行本地日志记录，你可能觉得该名称很眼熟。 单击“EventClass**s”或某个事件名称，Log Analytics 工作区将继续构造查询\_** 。 请务必保存查询以便稍后重复使用。

### <a name="example-queries"></a>查询示例

#### <a name="example-1"></a>示例 1

以下查询返回模型数据库和服务器的每个查询结束/刷新结束事件的持续时间。 如果进行横向扩展，则结果将按副本细分，因为副本编号包含在 ServerName_s 中。 按 RootActivityId_g 分组可减少从 Azure 诊断 REST API 检索到的行数，并有助于保持在 [Log Analytics 速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)中所述的限制内。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>示例 2

以下查询返回服务器的内存和 QPU 消耗。 如果进行横向扩展，则结果将按副本细分，因为副本编号包含在 ServerName_s 中。

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>示例 3

以下查询返回服务器的 Analysis Services 引擎性能计数器每秒读取的行数。

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

有成百上千的查询可以使用。 若要了解有关查询的详细信息，请参阅 [Azure Monitor 日志查询入门](../azure-monitor/log-query/get-started-queries.md)。


## <a name="turn-on-logging-by-using-powershell"></a>使用 PowerShell 启用日志记录

在此快速教程中，你将在 Analysis Services 服务器所在订阅和资源组中创建存储帐户。 然后使用 Set-AzDiagnosticSetting 启用诊断日志记录，将输出发送到新的存储帐户。

### <a name="prerequisites"></a>先决条件
要完成本教程，必须备好以下资源：

* 现有 Azure Analysis Services 服务器。 有关创建服务器资源的说明，请参阅[在 Azure 门户中创建服务器](analysis-services-create-server.md)或[使用 PowerShell 创建 Azure Analysis Services 服务器](analysis-services-create-powershell.md)。

### <a name="aconnect-to-your-subscriptions"></a></a>连接到订阅

启动 Azure PowerShell 会话，然后使用以下命令登录 Azure 帐户：  

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建 Azure 密钥保管库的特定订阅。 键入以下命令查看帐户的订阅：

```powershell
Get-AzSubscription
```

然后，键入以下命令，以指定与要记录的 Azure Analysis Services 帐户关联的订阅：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 如果帐户关联了多个订阅，请务必指定该订阅。
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>为日志创建新的存储帐户

如果服务器所属订阅中存在现有存储帐户，可对日志使用该帐户。 对本教程而言，需创建专用于 Analysis Services 日志的新存储帐户。 为方便起见，将存储帐户详细信息将存储到名为 sa 的变量中。

还要使用包含 Analysis Services 服务器的资源组。 将 `awsales_resgroup`、`awsaleslogs` 和 `West Central US` 的值替换为自己的值：

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>标识用于你的日志的服务器帐户

将帐户名称设置为名为“account”的变量，其中 ResourceName 是帐户的名称。

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>启用日志记录

若要启用日志记录，请将 Set-AzDiagnosticSetting cmdlet 与新存储帐户、服务器帐户和类别的变量一起使用。 运行以下命令，将“-Enabled”标志设置为“$true”：

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

输出应该类似于以下示例：

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

此输出确认已为服务器启用日志记录，会将信息保存到存储帐户。

还可为日志设置保留策略，以便自动删除较旧的日志。 例如，使用 **-RetentionEnabled** 标志将保留期策略设置为 **$true**，并将 **-RetentionInDays** 参数设置为 **90**。 将自动删除超过 90 天的日志。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>后续步骤

深入了解 [Azure 资源诊断日志记录](../azure-monitor/platform/resource-logs-overview.md)

请参阅 PowerShell 帮助中的 [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)。
