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
ms.date: 07/05/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed3bd763edb94d7bea28b4039c03afa7359fee1


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Log Analytics 中的 Azure 网络分析（预览）解决方案
> [!NOTE]
> 这是[预览解决方案](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)。
> 
> 

可以使用 Log Analytics 中的 Azure 网络分析解决方案，查看 Azure 应用程序网关日志和 Azure 网络安全组日志。

可以为 Azure 应用程序网关日志和 Azure 网络安全组启用日志记录。 这些日志将写入到 Blob 存储，然后 Log Analytics 可以从中为其编制索引，用于搜索和分析。

应用程序网关支持以下日志：

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

网络安全组支持以下日志：

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明安装和配置 Azure 网络分析解决方案：

1. 为需要监视的资源启用诊断日志记录：
   * [应用程序网关](../application-gateway/application-gateway-diagnostics.md)
   * [网络安全组](../virtual-network/virtual-network-nsg-manage-log.md)
2. 使用 [blob 存储中的 JSON 文件](log-analytics-azure-storage-json.md)中所述的过程，将 Log Analytics 配置为从 Blob 存储中读取日志。
3. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure 网络分析解决方案。  

如果未针对特定资源类型启用诊断日志记录，该资源的仪表板边栏选项卡将为空白。

## <a name="review-azure-networking-analytics-data-collection-details"></a>查看 Azure 网络分析数据收集详细信息
Azure 网络分析解决方案从 Azure 应用程序网关和网络安全组的 Azure Blob 存储中收集诊断数据。
数据收集无需代理。

下表显示了数据收集方法，以及有关如何为 Azure 网络分析收集数据的其他详细信息。

| 平台 | 直接代理 | Systems Center Operations Manager (SCOM) 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
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




<!--HONumber=Nov16_HO3-->


