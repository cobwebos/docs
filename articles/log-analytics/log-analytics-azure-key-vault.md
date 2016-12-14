---
title: "Log Analytics 中的 Azure 密钥保管库解决方案 | Microsoft Docs"
description: "可以在 Log Analytics 中使用 Azure 密钥保管库解决方案来查看 Azure 密钥保管库日志。"
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 90ef2d32a00744decdb5a50ae1f707820e87f513


---
# <a name="azure-key-vault-preview-solution-in-log-analytics"></a>Log Analytics 中的 Azure 密钥保管库（预览版）解决方案
> [!NOTE]
> 这是[预览解决方案](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)。
> 
> 

你可以在 Log Analytics 中使用 Azure 密钥保管库解决方案来查看 Azure 密钥保管库 AuditEvent 日志。

可以为 Azure 密钥保管库启用审核事件的日志记录。 这些日志将被写入到 Azure Blob 存储，然后 Log Analytics 可以从中对其进行索引，用于搜索和分析。

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明来安装和配置 Azure 密钥保管库解决方案：

1. 启用要监视的[密钥保管库的诊断日志记录](../key-vault/key-vault-logging.md)资源
2. 通过使用 [blob 存储中的 JSON 文件](log-analytics-azure-storage-json.md)中所述的过程，配置 Log Analytics，以从 blob 存储中读取日志。
3. 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，启用 Azure 密钥保管库解决方案。  

## <a name="review-azure-key-vault-data-collection-details"></a>查看 Azure 密钥保管库数据集合详细信息
Azure 密钥保管库解决方案将从 Azure blob 存储中收集用于 Azure 密钥保管库的诊断日志。
数据收集无需代理。

下表显示了数据收集方法，以及有关如何为 Azure 密钥保管库收集数据的其他详细信息。

| 平台 | 直接代理 | Systems Center Operations Manager (SCOM) 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![是](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![否](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |10 分钟 |

## <a name="use-azure-key-vault"></a>使用 Azure 密钥保管库
在安装解决方案后，可以通过使用 Log Analytics“**概述**”页上的“**Azure 密钥保管库**”磁贴，查看受监视的密钥保管库在一段时间内的请求状态摘要。

![Azure 密钥保管库磁贴的图像](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

单击“**概述**”磁贴后，可以查看日志摘要，然后钻取以下类别的详细信息：

* 一段时间内的所有密钥保管库操作量
* 一段时间内失败的操作量
* 按操作显示的平均操作延迟
* 对于操作数目需要超过 1000 毫秒和操作列表需超过 1000 毫秒的此类操作的服务质量

![Azure 密钥保管库仪表板的图像](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure 密钥保管库仪表板的图像](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>查看任何操作的详细信息
1. 在“**概述**”页上，单击“**Azure 密钥保管库**”磁贴。
2. 在“**Azure 密钥保管库**”仪表板中，查看其中一个边栏选项卡中的摘要信息，然后单击一个以在日志搜索页查看其详细信息。
   
    在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 你也可以按方面进行筛选以缩减搜索结果。

## <a name="log-analytics-records"></a>Log Analytics 记录
Azure 密钥保管库解决方案可分析从 Azure 诊断中的 [AuditEvent 日志](../key-vault/key-vault-logging.md)收集的类型为 **KeyVaults** 的记录。  这些记录的属性如下表中所列。  

| 属性 | 说明 |
|:--- |:--- |
| 类型 |*KeyVaults* |
| SourceSystem |*AzureStorage* |
| CallerIpAddress |发出请求的客户端 IP 地址 |
| 类别 |对于密钥保管库日志而言，AuditEvent 是唯一可用值。 |
| CorrelationId |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端（密钥保管库）日志相关联。 |
| DurationMs |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| HttpStatusCode_d |由请求返回的 HTTP 状态代码 |
| Id_s |请求的唯一 ID |
| Identity_o |发出 REST API 请求时提供的令牌中的标识。 与通过 Azure PowerShell cmdlet 发出请求一样，这通常是“用户”、“服务主体”，或者“用户+应用程序 ID”的组合。 |
| OperationName |[Azure 密钥保管库日志记录](../key-vault/key-vault-logging.md)中所述操作的名称 |
| OperationVersion |客户端请求的 REST API 版本 |
| RemoteIPLatitude |发出请求的客户端纬度 |
| RemoteIPLongitude |发出请求的客户端经度 |
| RemoteIPCountry |发出请求的客户端国家/地区 |
| RequestUri_s |请求的 URI |
| 资源 |密钥保管库的名称 |
| resourceGroup |密钥保管库的资源组 |
| ResourceId |Azure 资源管理器资源 ID。 对于密钥保管库日志而言，这始终是密钥保管库资源 ID。 |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResultSignature |HTTP 状态 |
| ResultType |REST API 请求的结果 |
| SubscriptionId |包含密钥保管库的订阅的 Azure 订阅 ID |

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看 Azure 密钥保管库的详细数据。




<!--HONumber=Nov16_HO3-->


