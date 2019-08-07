---
title: Azure 存储的高级威胁防护
description: 配置 Azure 存储高级威胁防护以检测帐户活动中的异常情况，并通知用户可能有害的访问帐户尝试。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782580"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Azure 存储的高级威胁防护

Azure 存储高级威胁防护提供额外的一层安全智能，用于检测试图访问或利用存储帐户的异常或可能有害的企图。 借助此保护层，用户无需成为安全专家，也无需管理安全监视系统便可以解决威胁。 

当发生异常时, 将触发安全警报。  这些安全警报与[Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成, 还会通过电子邮件发送给订阅管理员, 并详细介绍了可疑活动以及如何调查和修正威胁的建议。

> [!NOTE]
> * Azure 存储的高级威胁防护目前仅适用于 Blob 存储。
> * 有关定价详细信息 (包括免费的30天试用版), 请参阅[Azure 安全中心定价页]( https://azure.microsoft.com/pricing/details/security-center/)。
> * Azure 存储的 ATP 功能当前在 Azure 政府版和主权的云区域中不可用。

适用于 Azure 存储的高级威胁防护引入对 Blob 存储的读取、写入和删除请求的诊断日志进行威胁检测。 若要调查高级威胁防护中的警报, 可以使用存储分析日志记录查看相关的存储活动。 有关详细信息, 请参阅如何[配置存储分析日志记录](storage-monitor-storage-account.md#configure-logging)。

## <a name="set-up-advanced-threat-protection"></a>设置高级威胁防护 

### <a name="using-the-portal"></a>使用门户

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中启动 Azure 门户。

2. 导航到想要保护的 Azure 存储帐户的配置页面。 在“设置”页面中，选择“高级威胁防护”。

3. 在“高级威胁防护”配置边栏选项卡中
    * **打开**高级*威胁防护*
    * 单击“保存”以保存新的或更新的高级威胁防护策略。 （图像中的价格仅用于示例目的。）

![打开 Azure 存储高级威胁防护](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>使用 Azure 安全中心

当你订阅 Azure 安全中心的 "标准" 层时, 会自动对所有存储帐户设置高级威胁防护。 你可以在特定订阅下为你的存储帐户启用或禁用高级威胁防护, 如下所示:

1. 在[Azure 门户](https://portal.azure.com)中启动**Azure 安全中心**。
1. 在主菜单中, 单击 "**定价 & 设置**"。
1. 单击要为其存储帐户启用或禁用威胁防护的订阅。

    ![选择订阅](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 单击“定价层”。
1. 在 "**按资源类型选择定价层**" 部分的 "**存储帐户**" 行中, 单击 "**已启用**" 或 "**已禁用**"。

    ![在安全中心启用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 单击“保存”。

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

使用 Azure 资源管理器模板来部署启用了高级威胁防护的 Azure 存储帐户。 有关详细信息, 请参阅[具有高级威胁防护的存储帐户](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="using-azure-policy"></a>使用 Azure 策略

使用 Azure 策略在特定订阅或资源组中的存储帐户之间启用高级威胁防护。

1. 启动 "Azure**策略-定义**" 页。

1. 搜索 "**部署高级威胁防护存储帐户**" 策略。

     ![搜索策略](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. 选择 Azure 订阅或资源组。

    ![选择订阅或组](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 分配策略。

    !["策略定义" 页](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>使用 REST API
使用 Rest API 命令创建、更新或获取特定存储帐户的高级威胁防护设置。

* [高级威胁防护-创建](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [高级威胁防护-获取](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用以下 PowerShell cmdlet:

  * [启用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [获取高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [禁用高级威胁防护](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>探索安全异常

存储活动出现异常时，用户会收到一封电子邮件通知，其中包含有关可疑安全事件的信息。 事件详细信息包括：

* 异常的性质
* 存储帐户名称
* 事件时间
* 存储类型
* 可能的原因 
* 调查步骤
* 修正步骤


电子邮件还包含有关可能原因的详细信息以及用于调查和缓解潜在威胁的建议操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

可从 Azure 安全中心的[安全警报磁贴](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)查看和管理当前安全警报。 单击特定警报可提供详细信息以及用于调查当前威胁和解决潜在威胁的操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>保护警报

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 有关这些警报的列表, 请参阅[Azure 存储](../../security-center/security-center-alerts-data-services.md#azure-storage)警报

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 存储帐户中的日志的](/rest/api/storageservices/About-Storage-Analytics-Logging)详细信息

* 了解有关 [Azure 安全中心](../../security-center/security-center-intro.md)的详细信息
