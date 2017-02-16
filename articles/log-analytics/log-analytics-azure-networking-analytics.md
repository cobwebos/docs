---
title: "Log Analytics 中的 Azure 网络分析解决方案 | Microsoft Docs"
description: "可以使用 Log Analytics 中的 Azure 网络分析解决方案，查看 Azure 网络安全组日志和 Azure 应用程序网关日志。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/31/2017
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a5d0587a9b7c6ea228caa8ab23307f5f46d122ef
ms.openlocfilehash: 840239d3bc7fd8c2ce68d0cce15f844c1f2693e7


---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Log Analytics 中的 Azure 网络监视解决方案

可以使用 Log Analytics 中的 Azure 应用程序网关分析解决方案来查看：

* Azure 应用程序网关日志
* Azure 应用程序网关指标

可以使用 Log Analytics 中的 Azure 网络安全组分析解决方案来查看：

* Azure 网络安全组日志

> [!NOTE]
> Azure 应用程序网关分析和 Azure 网络安全组分析为[预览版解决方案](log-analytics-add-solutions.md#preview-management-solutions-and-features)。
> 
> 

若要使用该解决方案，请启用 Azure 应用程序网关日志和 Azure 网络安全组的诊断，并将诊断引导至 Log Analytics 工作区。 不需要将日志写入 Azure Blob 存储。

可为应用程序网关和/或网络安全组启用诊断和相应的解决方案。

如果未针对特定资源类型启用诊断日志记录但安装了解决方案，该资源的仪表板边栏选项卡将为空白并显示错误消息。

> [!NOTE]
> 2017 年 1 月，将应用程序网关和网络安全组中的日志发送到 Log Analytics 的受支持方式已发生变化。 如果看到了“Azure 网络分析(已弃用)”解决方案，请参阅 [migrating from the old Networking Analytics solution](#migrating-from-the-old-networking-analytics-solution)（从旧的网络分析解决方案迁移）了解需要执行的步骤。
>
>

## <a name="review-azure-networking-data-collection-details"></a>查看 Azure 网络数据收集详细信息
Azure 应用程序网关分析和网络安全组分析管理解决方案直接从 Azure 应用程序网关和网络安全组收集诊断日志。 不需要将日志写入 Azure Blob 存储，且数据收集无需代理。

下表显示了数据收集方法，以及有关如何为 Azure 应用程序网关分析和网络安全组分析收集数据的其他详细信息。

| 平台 | 直接代理 | Systems Center Operations Manager 代理 | Azure | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![是](./media/log-analytics-azure-networking/oms-bullet-green.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |登录时 |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Log Analytics 中的 Azure 应用程序网关分析解决方案

应用程序网关支持以下日志：

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

应用程序网关支持以下指标：

* 5 分钟吞吐量

### <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明安装并配置 Azure 应用程序网关分析解决方案：

1. 为需要监视的[应用程序网关](../application-gateway/application-gateway-diagnostics.md)启用诊断日志记录。
2. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure 应用程序网关分析解决方案。 

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>在门户中启用 Azure 应用程序网关诊断

1. 在 Azure 门户中，导航到要监视的应用程序网关资源
2. 选择“诊断日志”打开以下页面

   ![Azure 应用程序网关资源图像](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. 单击“打开诊断”打开以下页面

   ![Azure 应用程序网关资源图像](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. 若要打开诊断，请单击“状态”下面的“打开”
5. 单击“发送到 Log Analytics”对应的复选框
6. 选择现有的 Log Analytics 工作区，或创建一个工作区
7. 对于要收集的每种日志类型，请单击“日志”下面的复选框
8. 单击“保存”，启用在 Log Analytics 中记录诊断日志

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>使用 PowerShell 启用 Azure 网络诊断

以下 PowerShell 脚本提供如何为应用程序网关启用诊断日志记录的示例。

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>使用 Azure 应用程序网关分析
![Azure 应用程序网关分析磁贴图像](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

在“概述”中单击“Azure 应用程序网关分析”磁贴后，可以查看日志摘要，然后钻取以下类别的详细信息：

* 应用程序网关访问日志
  * 应用程序网关访问日志的客户端和服务器错误
  * 每个应用程序网关每小时请求数
  * 每个应用程序网关每小时失败请求数
  * 应用程序网关的用户代理引起的错误
* 应用程序网关性能
  * 应用程序网关的主机运行状况
  * 最大和 95% 应用程序网关失败请求

![Azure 应用程序网关分析仪表板图像](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Azure 应用程序网关分析仪表板图像](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

在“Azure 应用程序网关分析”仪表板上，查看其中一个边栏选项卡中的摘要信息，然后单击一项摘要，在日志搜索页查看其详细信息。
   
在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 还可以按方面进行筛选以缩减搜索结果。


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Log Analytics 中的 Azure 网络安全组分析解决方案

网络安全组支持以下日志：

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

### <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明安装和配置 Azure 网络分析解决方案：

1. 为想要监视的[网络安全组](../virtual-network/virtual-network-nsg-manage-log.md)资源启用诊断日志记录。
2. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure 网络安全组分析解决方案。 

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>在门户中启用 Azure 网络安全组诊断

1. 在 Azure 门户中，导航到要监视的网络安全组资源
2. 选择“诊断日志”打开以下页面

   ![Azure 网络安全组资源图像](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. 单击“打开诊断”打开以下页面

   ![Azure 网络安全组资源图像](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. 若要打开诊断，请单击“状态”下面的“打开”
5. 单击“发送到 Log Analytics”对应的复选框
6. 选择现有的 Log Analytics 工作区，或创建一个工作区
7. 对于要收集的每种日志类型，请单击“日志”下面的复选框
8. 单击“保存”，启用在 Log Analytics 中记录诊断日志

### <a name="enable-azure-network-diagnostics-using-powershell"></a>使用 PowerShell 启用 Azure 网络诊断

以下 PowerShell 脚本提供如何为网络安全组启用诊断日志记录的示例 
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>使用 Azure 网络安全组分析
在“概述”中单击“Azure 网络安全组分析”磁贴后，可以查看日志摘要，然后钻取以下类别的详细信息：

* 网络安全组阻止的流
  * 具有阻止流的网络安全组规则
  * 具有阻止流的 MAC 地址
* 网络安全组允许的流
  * 具有允许流的网络安全组规则
  * 具有允许流的 MAC 地址

![Azure 网络安全组分析仪表板图像](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Azure 网络安全组分析仪表板图像](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

在“Azure 网络安全组分析”仪表板上，查看其中一个边栏选项卡中的摘要信息，然后单击一项摘要，在日志搜索页查看其详细信息。
   
在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 还可以按方面进行筛选以缩减搜索结果。

## <a name="migrating-from-the-old-networking-analytics-solution"></a>从旧的网络分析解决方案迁移
2017 年 1 月，将 Azure 应用程序网关和 Azure 网络安全组中的日志发送到 Log Analytics 的受支持方式已发生更改。 这些更改带来了以下优势：
+ 日志将直接写入 Log Analytics，无需使用存储帐户
+ 从生成日志到在 Log Analytics 中显示日志的延迟时间已缩短
+ 配置步骤更少
+ 所有类型的 Azure 诊断的通用格式

若要使用更新的解决方案，请执行以下操作：

1. [将诊断配置为直接从 Azure 应用程序网关发送到 Log Analytics](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [将诊断配置为直接从 Azure 网络安全组发送到 Log Analytics](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 *Azure 应用程序网关分析*和 *Azure 网络安全组分析*解决方案。
3. 更新所有已保存的查询、仪表板或警报，以使用的新数据类型
  + 新类型为 AzureDiagnostics。 可以使用 ResourceType 筛选 Azure 网络日志。
  
    | 不要使用： | 使用： |
    | --- | --- |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayAccess`| `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayAccess` |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayPerformance` | `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayPerformance` |
    | `Type=NetworkSecuritygroups` | `Type=AzureDiagnostics ResourceType=NETWORKSECURITYGROUPS` |
    
   + 对于名称中包含 \_s、\_d 或 \_g 后缀的任何字段，请将第一个字符更改为小写
   + 对于名称中包含 \_o 后缀的任何字段，数据将根据嵌套的字段名称拆分为单个字段。
4. 删除“Azure 网络分析(已弃用)”解决方案。 
  + 如果使用的是 PowerShell，请使用 `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false` 

在发生此项更改之前收集的数据不会显示在新解决方案中。 可以继续使用旧类型和字段名称查询此数据。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看详细的 Azure 诊断数据。




<!--HONumber=Feb17_HO1-->


