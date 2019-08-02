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
ms.openlocfilehash: bb2d5733704b0b31dc010cec2a90e99e1be07b56
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592029"
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

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 这些事件可以触发以下警报：

### <a name="anomalous-access-pattern-alerts"></a>异常访问模式警报

* **从异常位置访问**:当某个用户从异常地理位置访问了存储帐户时, 将触发此警报。
可能的原因:
   * 攻击者已访问你的存储帐户
   * 合法用户已从新位置访问你的存储帐户
 
* **应用程序异常**:此警报表示异常应用程序已访问此存储帐户。 可能的原因:
   * 攻击者使用新的应用程序访问了你的存储帐户。
   * 合法用户使用了新的应用程序/浏览器来访问你的存储帐户。

* **匿名访问**:此警报表示此帐户已匿名访问 (即, 没有任何身份验证), 这与此帐户上的最新访问模式有意外的对比。
可能的原因:
   * 攻击者已利用容器的公共读取访问权限。
   * 合法用户或应用程序已使用容器的公共读取访问权限。

* **Tor 异常**:此警报表示已成功从称为 Tor 的活动退出节点的 IP 地址 (匿名代理) 访问此帐户。 此警报的严重性考虑使用的身份验证类型 (如果有), 以及这是否是此类访问的第一种情况。
可能的原因:
   * 攻击者使用 Tor 访问了你的存储帐户。
   * 合法用户使用 Tor 访问了你的存储帐户。


### <a name="anomalous-extractupload-alerts"></a>异常提取/上传警报

* **数据渗透**:此警报表明与此存储容器上的最近活动相比, 已经提取了异常大量的数据。 可能的原因:
   * 攻击者已从容器提取大量数据。 (例如: data 渗透/违例、未经授权的数据传输)
   * 合法用户或应用程序已从容器提取了异常数据量。 (例如: 维护活动)

* **意外删除**:此警报表示存储帐户中发生了一个或多个意外的删除操作, 与此帐户上的最近活动相比。 可能的原因:
   * 攻击者已从你的存储帐户中删除数据。
   * 合法用户执行了异常删除。

* **上载 Azure 云服务包**:此警报表示 Azure 云服务包 (.cspkg 文件) 已以异常方式上传到存储帐户, 与此帐户上的最近活动相比。 可能的原因: 
   * 攻击者已准备好将恶意代码从你的存储帐户部署到 Azure 云服务。
   * 合法用户已准备好进行合法的服务部署。

### <a name="suspicious-storage-activities-alerts"></a>可疑的存储活动警报

* **访问权限更改**:此警报表示此存储容器的访问权限已以异常方式更改。 可能的原因: 
   * 攻击者已更改容器权限来削弱其安全性。
   * 合法用户已更改容器权限。

* **访问检查**:此警报表示与此帐户上的最近活动相比, 以异常方式检查过存储帐户的访问权限。 可能的原因: 
   * 攻击者已执行侦测, 以应对未来的攻击。
   * 合法用户已对存储帐户执行维护。

* **数据浏览**:此警报表示存储帐户中的 blob 或容器已被异常地枚举, 与此帐户上的最近活动相比。 可能的原因: 
   * 攻击者已执行侦测, 以应对未来的攻击。
   * 合法用户或应用程序逻辑已浏览存储帐户中的数据。






## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 存储帐户中的日志的](/rest/api/storageservices/About-Storage-Analytics-Logging)详细信息

* 了解有关 [Azure 安全中心](../../security-center/security-center-intro.md)的详细信息
