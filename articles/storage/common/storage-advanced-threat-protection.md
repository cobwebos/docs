---
title: 配置高级威胁防护
titleSuffix: Azure Storage
description: 为 Azure 存储配置高级威胁保护，以检测帐户活动中的异常，并通知访问帐户的潜在有害尝试。
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061302"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>为 Azure 存储配置高级威胁保护

Azure 存储的高级威胁保护提供了额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 此保护层允许您解决威胁，而无需成为安全专家或管理安全监控系统。

当活动异常发生时，将触发安全警报。 这些安全警报与 Azure[安全中心](https://azure.microsoft.com/services/security-center/)集成，并且也通过电子邮件发送给订阅管理员，其中详细介绍了可疑活动，并就如何调查和修复威胁提出了建议。

> [!NOTE]
> Azure 存储的高级威胁保护目前仅适用于 Blob 存储。 它在 Azure 政府和主权云区域中不可用。 有关定价详细信息（包括 30 天免费试用版），请参阅[Azure 安全中心定价页面]( https://azure.microsoft.com/pricing/details/security-center/)。

Azure 存储的高级威胁保护可引入读取、写入和删除到 Blob 存储的请求以检测威胁的诊断日志。 要调查来自高级威胁防护的警报，可以使用存储分析日志记录查看相关的存储活动。 有关详细信息，请参阅在[Azure 门户中](storage-monitor-storage-account.md#configure-logging)**配置日志记录**。

## <a name="set-up-advanced-threat-protection"></a>设置高级威胁防护

您可以通过以下几节中介绍的几种方式配置高级威胁防护。

### <a name="portal"></a>[门户](#tab/azure-portal)

1. 启动 [Azure 门户](https://portal.azure.com/)。
1. 导航到 Azure 存储帐户。 在 **"设置"** 下，选择 **"高级安全**"。
1. 选择高级安全配置页面上的 **"设置"** 链接。
1. 将**高级安全性**设置为**ON**。
1. 单击 **"保存**"以保存新策略或更新的策略。

    ![打开 Azure 存储高级威胁防护](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure 安全中心](#tab/azure-security-center)

订阅 Azure 安全中心中的标准层时，所有存储帐户上会自动设置高级威胁保护。 您可以在特定订阅下为存储帐户启用或禁用高级威胁保护，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中启动**Azure 安全中心**。
1. 在主菜单中，单击 **"定价&设置**。
1. 单击要为其存储帐户启用或禁用威胁保护的订阅。

    ![选择订阅](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 单击“定价层”****。
1. 在"**按资源类型选择定价层"** 部分中，在 **"存储帐户**"行中，单击"**已启用**"或 **"已禁用**"。

    ![在安全中心启用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 单击“保存”。****

### <a name="template"></a>[模板](#tab/template)

使用 Azure 资源管理器模板部署启用了高级威胁保护的 Azure 存储帐户。 有关详细信息，请参阅[具有高级威胁保护的存储帐户](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="azure-policy"></a>[Azure 策略](#tab/azure-policy)

使用 Azure 策略在特定订阅或资源组下跨存储帐户启用高级威胁保护。

1. 启动 Azure**策略 - 定义**页。

1. 搜索**存储帐户策略上的"部署高级威胁保护**"。

     ![搜索策略](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. 选择 Azure 订阅或资源组。

    ![选择订阅或组](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 分配策略。

    ![策略定义页面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

使用 Rest API 命令为特定存储帐户创建、更新或获取高级威胁保护设置。

* [高级威胁防护 - 创建](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [高级威胁防护 - 获取](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

使用以下 PowerShell cmdlet：

* [启用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [获得高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [禁用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>探索安全异常

存储活动出现异常时，用户会收到一封电子邮件通知，其中包含有关可疑安全事件的信息。 事件详细信息包括：

* 异常的性质
* 存储帐户名称
* 活动时间
* 存储类型
* 潜在原因
* 调查步骤
* 补救步骤

电子邮件还包含有关可能原因的详细信息以及用于调查和缓解潜在威胁的建议操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

可以从 Azure 安全中心[的安全警报磁贴](../../security-center/security-center-managing-and-responding-alerts.md)查看和管理当前安全警报。 单击特定警报可提供详细信息以及用于调查当前威胁和解决潜在威胁的操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>安全警报

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 有关 Azure 存储的警报列表，请参阅[Azure 安全中心中数据服务的威胁保护中的](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)**存储**部分。

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 存储帐户中的日志](/rest/api/storageservices/About-Storage-Analytics-Logging)详细信息
* 了解有关[Azure 安全中心](../../security-center/security-center-intro.md)
