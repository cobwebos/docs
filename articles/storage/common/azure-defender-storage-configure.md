---
title: 配置用于存储的 Azure Defender
titleSuffix: Azure Storage
description: 配置用于存储的 Azure Defender 以检测帐户活动中的异常，并通知可能有害的访问帐户尝试。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: c7e0c9aee1ce6b4a2524ac756673784b63be3b31
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289763"
---
# <a name="configure-azure-defender-for-storage"></a>配置用于存储的 Azure Defender

用于存储的 Azure Defender 提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 这一层保护使你可以在不是安全专家或管理安全监视系统的情况下解决威胁。

当活动出现异常时，会触发安全警报。 这些安全警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，还会通过电子邮件发送给订阅管理员，并详细介绍了可疑活动以及如何调查和修正威胁的建议。

服务引入对 Blob 存储和 Azure 文件的读取、写入和删除请求的资源日志，用于威胁检测。 若要调查来自 Azure Defender 的警报，可以使用存储分析日志记录查看相关的存储活动。 有关详细信息，请参阅在[Azure 门户中监视存储帐户](storage-monitor-storage-account.md#configure-logging)中的 "**配置日志记录**"。

## <a name="availability"></a>可用性

适用于存储的 azure Defender 目前可用于 Blob 存储、Azure 文件和 Azure Data Lake Storage Gen2。 支持 Azure Defender 的帐户类型包括常规用途 v2、块 blob 和 Blob 存储帐户。 适用于存储的 Azure Defender 适用于所有公有云和美国政府云，但不适用于其他主权或 Azure 政府版云区域。

为 Data Lake Storage 启用分层命名空间的帐户使用 Azure Blob 存储 Api 和 Data Lake Storage Api 支持事务。 Azure 文件共享通过 SMB 支持事务。

如需定价详细信息（包括 30 天免费试用版的信息），请参阅 [Azure 安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

以下列表汇总了用于存储的 Azure Defender 的可用性：

- 发布状态：
  - [Blob 存储](https://azure.microsoft.com/services/storage/blobs/) (公开上市) 
  - [Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (公开发布) 
  - Azure Data Lake Storage Gen2 (公开上市) 
- 云：<br>
    ✔ 商业云<br>
    ✔ US Gov<br>
    ✘中国 Gov，其他 Gov

## <a name="set-up-azure-defender"></a>设置 Azure Defender

可以通过多种方式来配置用于存储的 Azure Defender，如以下部分中所述。

### <a name="azure-security-center"></a>[Azure 安全中心](#tab/azure-security-center)

当你订阅 Azure 安全中心的标准层时，Azure Defender 会自动设置在你的所有存储帐户上。 可以在特定订阅下为存储帐户启用或禁用 Azure Defender，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中启动**Azure 安全中心**。
1. 在主菜单中的 " **管理**" 下，选择 " **定价 & 设置**"。
1. 选择要为其启用或禁用 Azure Defender 的订阅。
1. 选择 " **打开 Azure defender** "，为订阅启用 azure defender。
1. 在 "**按资源类型选择 Azure Defender 计划**" 下，找到 "**存储**" 行，然后在 "**计划**" 列中选择 "**已启用**"。
1. 保存所做更改。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="显示如何在安全中心启用 Azure Defender 以实现存储的屏幕截图":::

现在已为此订阅中的所有存储帐户启用 Azure Defender。

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 启动 [Azure 门户](https://portal.azure.com/)。
1. 导航到存储帐户。 在 " **设置**" 下，选择 " **高级安全**"。
1. 选择 " **启用用于存储的 Azure Defender**"。

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="显示如何为 Azure 存储帐户启用 Azure Defender 的屏幕截图":::

现在为此存储帐户启用了 Azure Defender。

### <a name="template"></a>[模板](#tab/template)

使用 Azure 资源管理器模板来部署启用了 Azure Defender 的 Azure 存储帐户。 有关详细信息，请参阅 [具有高级威胁防护的存储帐户](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

使用 Azure 策略在特定订阅或资源组中的存储帐户之间启用 Azure Defender。

1. 启动 "Azure **策略-定义** " 页。
1. 搜索 " **部署 Azure Defender 存储帐户** " 策略。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="应用策略为存储帐户启用 Azure Defender":::

1. 选择 Azure 订阅或资源组。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="选择策略作用域的订阅或资源组 ":::

1. 分配策略。

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="分配策略以启用 Azure Defender 存储":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令创建、更新或获取特定存储帐户的 Azure Defender 设置。

- [高级威胁防护-创建](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [高级威胁防护-获取](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell cmdlet：

- [启用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [获取高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [禁用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全异常

存储活动出现异常时，用户会收到一封电子邮件通知，其中包含有关可疑安全事件的信息。 事件详细信息包括：

- 异常的性质
- 存储帐户名称
- 事件时间
- 存储类型
- 可能的原因
- 调查步骤
- 修正步骤

电子邮件还包含有关可能原因的详细信息以及用于调查和缓解潜在威胁的建议操作。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="用于存储的 Azure Defender 警报电子邮件":::

你可以从 Azure 安全中心的 " [安全警报" 磁贴](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理当前的安全警报。 单击特定警报可提供详细信息以及用于调查当前威胁和解决潜在威胁的操作。

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="用于存储的 Azure Defender 警报":::

## <a name="security-alerts"></a>安全警报

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 有关 Azure 存储的警报列表，请参阅 [Azure 存储的警报](../../security-center/alerts-reference.md#alerts-azurestorage)。

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 存储帐户中的日志的](/rest/api/storageservices/About-Storage-Analytics-Logging)详细信息
- 了解有关 [Azure 安全中心](../../security-center/security-center-intro.md)的详细信息
