---
title: "Azure 诊断日志概述 | Microsoft Docs"
description: "了解什么是 Azure 诊断日志，以及如何使用该诊断日志了解发生在 Azure 资源内的事件。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: df20e174abb9960ad378221008ac7261fd0582f1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>从 Azure 资源收集和使用日志数据

## <a name="what-are-azure-resource-diagnostic-logs"></a>什么是 Azure 资源诊断日志
**Azure 资源级诊断日志**是资源发出的日志，记录与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。 例如，网络安全组规则计数器和 Key Vault 审核是两种类别的资源日志。

资源级诊断日志与[活动日志](monitoring-overview-activity-logs.md)不同。 通过活动日志，可以深入了解使用资源管理器在订阅中的资源上执行的操作，例如创建虚拟机或删除逻辑应用。 活动日志是一个订阅级别的日志。 通过资源级诊断日志深入了解在资源本身内执行的操作，例如，从 Key Vault 获取机密。

资源级诊断日志来宾 OS 级诊断日志也不相同。 来宾 OS 级诊断日志是由在虚拟机内部或其他受支持的资源类型中运行的代理收集的日志。 资源级诊断日志不需要代理并从 Azure 平台本身捕获特定于资源的数据，而来宾 OS 级诊断日志从操作系统和在虚拟机上运行的应用程序捕获数据。

并非所有资源都支持此处所述的资源诊断日志新类型。 本文的某个部分列出了哪些资源类型支持新的资源级诊断日志。

![资源诊断日志与其他类型的日志 ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>可以对资源级诊断日志执行的操作
可以对资源诊断日志执行的部分操作如下：

![资源诊断日志的逻辑位置](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* 将诊断日志保存到[**存储帐户**](monitoring-archive-diagnostic-logs.md)进行审核或手动检查。 可以使用**资源诊断设置**指定保留时间（天）。
* [将诊断日志流式传输到**事件中心**](monitoring-stream-diagnostic-logs-to-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
* 使用 [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md) 对诊断日志进行分析

可以使用与发出日志的订阅不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有合适的 RBAC 访问权限。

## <a name="resource-diagnostic-settings"></a>资源诊断设置
非计算资源的资源诊断日志是使用资源诊断设置配置的。 资源控制的**资源诊断设置**：

* 将资源诊断日志和指标发送到何处：存储帐户、事件中心和/或 OMS Log Analytics。
* 发送哪些日志类别，是否也会发送指标数据。
* 应该将每个日志类别在存储帐户中保留多长时间
    - 保留期为零天表示日志将永久保留。 如果不需永久保留，则可将该值设置为 1 到 2147483647 之间的任意天数。
    - 如果设置了保留策略，但禁止将日志存储在存储帐户中（例如，如果仅选择事件中心或 OMS 选项），则保留策略无效。
    - 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。

这些设置可以通过诊断设置（适用于 Azure 门户中的资源）、Azure PowerShell 和 CLI 命令或 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx) 轻松进行配置。

> [!WARNING]
> 来自计算资源（例如，VM 或 Service Fabric）来宾 OS 层的诊断日志和指标使用[单独的输出配置和选择机制](../azure-diagnostics.md)。
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>如何启用资源诊断日志集合
可以在门户中通过该资源的页面[在资源管理器模板里创建资源的过程中](./monitoring-enable-diagnostic-logs-using-template.md)或在创建资源以后启用资源诊断日志的收集。 也可使用 Azure PowerShell 或 CLI 命令（或者使用 Azure Monitor REST API）在任何时间点启用集合。

> [!TIP]
> 这些说明可能不能直接应用到每个资源。 请参阅此页底部的架构链接，了解适用于特定资源类型的特殊步骤。
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>在门户中启用资源诊断日志的集合
可以在创建资源后，在 Azure 门户中通过转到特定的资源或导航到 Azure Monitor 来启用资源诊断日志的收集。 通过 Azure Monitor 启用日志收集：

1. 在 [Azure 门户](http://portal.azure.com)中，导航到 Azure Monitor 并单击“诊断设置”

    ![Azure Monitor 的“监视”部分](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. 为设置命名，选中要将数据发送到的每个目标对应的复选框，并配置要对每个目标使用哪个资源。 （可选）使用“保留期(天)”滑块设置这些日志的保留天数（仅适用于存储帐户目标）。 如果保留期为 0 天，则会无限期存储日志。
   
   ![添加诊断设置 - 现有的设置](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. 单击“ **保存**”。

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志发送到指定的目标。

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>通过 PowerShell 启用资源诊断日志的集合
若要通过 Azure PowerShell 启用资源诊断日志的集合，请使用以下命令：

若要允许在存储帐户中存储诊断日志，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

若要允许将诊断日志流式传输到事件中心，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

服务总线规则 ID 是以下格式的字符串：`{Service Bus resource ID}/authorizationrules/{key name}`。

若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

可以使用以下命令获取 Log Analytics 工作区的资源 ID：

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

可以组合这些参数以启用多个输出选项。

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>通过 CLI 启用资源诊断日志的集合
若要通过 Azure CLI 启用资源诊断日志的集合，请使用以下命令：

若要允许在存储帐户中存储诊断日志，请使用以下命令：

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

存储帐户 ID 是需要向其发送日志的存储帐户的资源 ID。

若要允许将诊断日志流式传输到事件中心，请使用以下命令：

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

服务总线规则 ID 是以下格式的字符串：`{Service Bus resource ID}/authorizationrules/{key name}`。

若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

可以组合这些参数以启用多个输出选项。

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>通过 REST API 启用资源诊断日志的集合
若要使用 Azure Monitor REST API 更改诊断设置，请参阅[此文档](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>在门户中管理资源诊断设置
确保使用诊断设置来设置所有资源。 在门户中导航到“监视”并打开“诊断设置”。

![门户中的“诊断日志”边栏选项卡](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

可能需要单击“所有服务”才能找到“监视”部分。

在此处可以查看和筛选所有支持诊断设置的资源，确定它们是否启用了诊断。 还可以向下钻取，以查看是否在某个资源上指定了多个设置，并检查这些数据流向哪个存储帐户、事件中心命名空间和/或 Log Analytics 工作区。

![门户中的“诊断日志”结果](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

添加诊断设置会打开“诊断设置”视图，可在其中启用、禁用或修改选定资源的诊断设置。

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>资源诊断日志支持的服务、类别和架构
[参阅此文](monitoring-diagnostic-logs-schema.md)获取受支持服务的完整列表，以及这些服务使用的日志类别和架构。

## <a name="next-steps"></a>后续步骤

* [将资源诊断日志流式传输到事件中心](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)
