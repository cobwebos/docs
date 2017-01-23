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
ms.date: 12/1/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a86819102797b0e243d28cd9ddb3d2c88c74bfca
ms.openlocfilehash: 7ea593885c1b380236a49ec030c00ad19097e2fa


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Log Analytics 中的 Azure 网络分析（预览）解决方案

可以使用 Log Analytics 中的 Azure 网络分析解决方案来查看：

* Azure 应用程序网关日志
* Azure 应用程序网关指标，以及 
* Azure 网络安全组日志。

> [!NOTE]
> Azure 网络分析是[预览版解决方案](log-analytics-add-solutions.md#preview-management-solutions-and-features)。
> 
> 

若要使用该解决方案，请启用 Azure 应用程序网关日志和 Azure 网络安全组的诊断，并将诊断引导到 Log Analytics 工作区。 不需要将日志写入 Azure Blob 存储。

应用程序网关支持以下日志：

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

应用程序网关支持以下指标：

* 5 分钟吞吐量

网络安全组支持以下日志：

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明安装和配置 Azure 网络分析解决方案：

1. 为需要监视的资源启用诊断日志记录：
   * [应用程序网关](../application-gateway/application-gateway-diagnostics.md)
   * [网络安全组](../virtual-network/virtual-network-nsg-manage-log.md)
2. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure 网络分析解决方案。  

以下 PowerShell 脚本提供如何启用应用程序网关和网络安全组的诊断日志记录的示例 
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```


如果未针对特定资源类型启用诊断日志记录，该资源的仪表板边栏选项卡将为空白。

## <a name="review-azure-networking-analytics-data-collection-details"></a>查看 Azure 网络分析数据收集详细信息
Azure 网络分析管理解决方案直接从 Azure 应用程序网关和网络安全组收集诊断日志。 不需要将日志写入 Azure Blob 存储，且数据收集无需代理。

下表显示了数据收集方法，以及有关如何为 Azure 网络分析收集数据的其他详细信息。

| 平台 | 直接代理 | Systems Center Operations Manager 代理 | Azure | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![是](./media/log-analytics-azure-networking/oms-bullet-green.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |![否](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 分钟 |

## <a name="use-azure-networking-analytics"></a>使用 Azure 网络分析
在安装解决方案后，可以使用 Log Analytics“概述”页上的“Azure 网络分析”磁贴，查看受监视应用程序网关的客户端和服务器错误摘要。

![Azure 网络分析磁贴的图像](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

单击“概述”磁贴后，可以查看日志摘要，然后钻取以下类别的详细信息：

* 应用程序网关访问日志
  * 应用程序网关访问日志的客户端和服务器错误
  * 每个应用程序网关每小时请求数
  * 每个应用程序网关每小时失败请求数
  * 应用程序网关的用户代理引起的错误
* 应用程序网关性能
  * 应用程序网关的主机运行状况
  * 最大和 95% 应用程序网关失败请求
* 网络安全组阻止的流
  * 具有阻止流的网络安全组规则
  * 具有阻止流的 MAC 地址
* 网络安全组允许的流
  * 具有允许流的网络安全组规则
  * 具有允许流的 MAC 地址

![Azure 网络分析仪表板的图像](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Azure 网络分析仪表板的图像](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Azure 网络分析仪表板的图像](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Azure 网络分析仪表板的图像](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>查看任何日志摘要的详细信息
1. 在“概述”页上，单击“Azure 网络分析”磁贴。
2. 在“Azure 网络分析”仪表板上，查看其中一个边栏选项卡中的摘要信息，然后单击一个以在日志搜索页查看其详细信息。
   
    在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 还可以按方面进行筛选以缩减搜索结果。

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看详细的 Azure 网络分析数据。




<!--HONumber=Dec16_HO1-->


