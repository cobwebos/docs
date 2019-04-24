---
title: Azure Monitor 中的 azure 密钥保管库解决方案 |Microsoft Docs
description: 可以在 Azure Monitor 中使用 Azure 密钥保管库解决方案来查看 Azure 密钥保管库日志。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 481b643f2f7201a2a1745c7aef9ddd81883da020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498310"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Monitor 中的 azure Key Vault 分析解决方案

![Key Vault 符号](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

可以在 Azure Monitor 中使用 Azure 密钥保管库解决方案来查看 Azure 密钥保管库 AuditEvent 日志。

要使用该解决方案，需要启用 Azure Key Vault 诊断的日志记录，并将诊断引导到 Log Analytics 工作区。 不需要将日志写入 Azure Blob 存储。

> [!NOTE]
> 2017 年 1 月，从 Key Vault 向 Log Analytics 发送日志的受支持方式已发生更改。 如果所用 Key Vault 解决方案的标题中演示了“(已弃用)”中，请参阅[从旧 Key Vault 解决方案迁移](#migrating-from-the-old-key-vault-solution)，了解需要执行的步骤。
>
>

## <a name="install-and-configure-the-solution"></a>安装和配置解决方案
使用以下说明来安装和配置 Azure 密钥保管库解决方案：

1. 使用中所述的过程[从解决方案库中的添加 Azure Monitor 解决方案](../../azure-monitor/insights/solutions.md)，将 Azure 密钥保管库解决方案添加到 Log Analytics 工作区。
2. 使用[门户](#enable-key-vault-diagnostics-in-the-portal)或 [PowerShell](#enable-key-vault-diagnostics-using-powershell) 为要监视的 Key Vault 资源启用诊断日志记录

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>在门户中启用 Key Vault 诊断

1. 在 Azure 门户中，导航到要监视的 Key Vault 资源
2. 选择*诊断设置*打开以下页面

   ![Azure 密钥保管库磁贴的图像](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. 单击“打开诊断”打开以下页面

   ![Azure 密钥保管库磁贴的图像](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. 为诊断设置的名称。
5. 单击“发送到 Log Analytics”对应的复选框
6. 选择现有的 Log Analytics 工作区，或创建一个工作区
7. 若要启用 *AuditEvent* 日志，请单击“日志”下面的复选框
8. 单击*保存*若要启用诊断以将 Log Analytics 工作区的日志记录。

### <a name="enable-key-vault-diagnostics-using-powershell"></a>使用 PowerShell 启用 Key Vault 诊断
以下 PowerShell 脚本提供如何使用 `Set-AzDiagnosticSetting` 为 Key Vault 启用诊断日志记录的示例：
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>查看 Azure 密钥保管库数据集合详细信息
Azure Key Vault 解决方案直接从 Key Vault 收集诊断日志。
不需要将日志写入 Azure Blob 存储，且数据收集无需代理。

下表显示了数据收集方法，以及有关如何为 Azure 密钥保管库收集数据的其他详细信息。

| 平台 | 直接代理 | Systems Center Operations Manager 代理 | Azure | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | 到达时 |

## <a name="use-azure-key-vault"></a>使用 Azure 密钥保管库
之后您[安装该解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)，通过单击查看密钥保管库数据**Key Vault 分析**磁贴从 Azure Monitor**概述**页。 通过点击“见解”部分下的“更多”，从“Azure Monitor”菜单打开此页面。 

![Azure 密钥保管库磁贴的图像](media/azure-key-vault/log-analytics-keyvault-tile.png)

单击后**Key Vault 分析**磁贴，可以查看日志摘要，并钻取以下类别的详细信息：

* 一段时间内的所有密钥保管库操作量
* 一段时间内失败的操作量
* 按操作显示的平均操作延迟
* 对于操作数目需要超过 1000 毫秒和操作列表需超过 1000 毫秒的此类操作的服务质量

![Azure 密钥保管库仪表板的图像](media/azure-key-vault/log-analytics-keyvault01.png)

![Azure 密钥保管库仪表板的图像](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>查看任何操作的详细信息
1. 上**概述**页上，单击**Key Vault 分析**磁贴。
2. 在“**Azure 密钥保管库**”仪表板中，查看其中一个边栏选项卡中的摘要信息，并单击一个以在日志搜索页查看其详细信息。

    在任何日志搜索页上，都可以按时间、详细结果和日志搜索历史记录查看结果。 也可以按方面进行筛选以缩减搜索结果。

## <a name="azure-monitor-log-records"></a>Azure Monitor 日志记录
Azure 密钥保管库解决方案可分析从 Azure 诊断中的 [AuditEvent 日志](../../key-vault/key-vault-logging.md)收集的类型为 **KeyVaults** 的记录。  这些记录的属性如下表中所列：  

| 属性 | 说明 |
|:--- |:--- |
| Type |AzureDiagnostics |
| SourceSystem |*Azure* |
| CallerIpAddress |发出请求的客户端 IP 地址 |
| 类别 | AuditEvent |
| CorrelationId |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端 (Key Vault) 日志相关联。 |
| DurationMs |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| httpStatusCode_d |由请求返回的 HTTP 状态代码（例如，200） |
| id_s |请求的唯一 ID |
| identity_claim_appid_g | 应用程序 ID 的 GUID |
| OperationName |[Azure 密钥保管库日志记录](../../key-vault/key-vault-logging.md)中所述操作的名称 |
| OperationVersion |客户端请求的 REST API 版本（例如，2015-06-01） |
| requestUri_s |请求的 URI |
| 资源 |密钥保管库的名称 |
| resourceGroup |密钥保管库的资源组 |
| ResourceId |Azure 资源管理器资源 ID。 对于 Key Vault 日志而言，这是 Key Vault 资源 ID。 |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | VAULTS |
| ResultSignature |HTTP 状态（例如，确定） |
| ResultType |REST API 请求的结果（例如，成功） |
| SubscriptionId |包含密钥保管库的订阅的 Azure 订阅 ID |

## <a name="migrating-from-the-old-key-vault-solution"></a>从旧 Key Vault 解决方案迁移
2017 年 1 月，从 Key Vault 向 Log Analytics 发送日志的受支持方式已发生更改。 这些更改带来了以下优势：
+ 日志将直接写入 Log Analytics 工作区中，而无需使用的存储帐户
+ 从生成日志到在 Log Analytics 中显示日志的延迟时间已缩短
+ 配置步骤更少
+ 所有类型的 Azure 诊断的通用格式

若要使用更新的解决方案，请执行以下操作：

1. [配置诊断以直接发送到 Log Analytics 工作区从密钥保管库](#enable-key-vault-diagnostics-in-the-portal)  
2. 通过使用中所述的过程中启用 Azure 密钥保管库解决方案[从解决方案库中的添加 Azure 监视解决方案](../../azure-monitor/insights/solutions.md)
3. 更新所有已保存的查询、仪表板或警报，以使用的新数据类型
   + 类型从KeyVaults 更改为 AzureDiagnostics。 可以使用 ResourceType 筛选 Key Vault 日志。
   + 不要使用 `KeyVaults`，应使用 `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + 字段：（字段名称区分大小写）
   + 对于名称中包含 \_s、\_d 或 \_g 后缀的任何字段，请将第一个字符更改为小写
   + 对于名称中包含 \_o 后缀的任何字段，数据会根据嵌套的字段名称拆分为单个字段。 例如，调用方的 UPN 存储在字段 `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s` 中
   + 字段 CallerIpAddress 已更改为 CallerIPAddress
   + 字段 RemoteIPCountry 不再存在
4. 删除“Key Vault Analytics (已弃用)”解决方案。 如果使用的是 PowerShell，请使用 `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

在发生此项更改之前收集的数据不会显示在新解决方案中。 可以继续使用旧类型和字段名称查询此数据。

## <a name="troubleshooting"></a>故障排除
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>后续步骤
* 使用[Azure Monitor 中的日志查询](../../azure-monitor/log-query/log-query-overview.md)若要查看 Azure 密钥保管库的详细的数据。
