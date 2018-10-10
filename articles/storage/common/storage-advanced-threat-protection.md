---
title: 监视 Azure 存储中的威胁
description: 配置 Azure 存储高级威胁防护以检测帐户活动中的异常情况，并通知用户可能有害的访问帐户尝试。
services: storage
author: rmatchoro
ms.service: storage
ms.topic: article
ms.date: 09/24/2018
ms.author: ronmat
ms.manager: shaik
ms.openlocfilehash: 8b2ca2d5d6418d68cab847df80fc437e468249ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995630"
---
# <a name="azure-storage-advanced-threat-protection"></a>Azure 存储高级威胁防护

Azure 存储高级威胁防护可检测帐户活动中的异常情况，并通知用户可能有害的访问帐户尝试。 借助此保护层，用户无需成为安全专家，也无需管理安全监视系统便可以解决威胁。

通过定义在活动出现异常时触发的安全警报来显示威胁。 这些警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包含可疑活动的详细信息以及如何调查和解决威胁的建议。 

> [!NOTE]
> Azure 存储高级威胁防护目前仅适用于 Blob 服务。 安全警报与 Azure 安全中心集成，并通过电子邮件发送给订阅管理员。

Azure 存储高级威胁防护会将读取、写入和删除请求的诊断日志引入 Blob 服务，以便进行威胁检测。 若要调查来自高级威胁防护的警报，需要[配置诊断日志](storage-monitor-storage-account.md#configure-logging)来为 Blob 服务启用所有级别的日志。

## <a name="set-up-advanced-threat-protection-in-the-portal"></a>在门户中设置高级威胁防护

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中启动 Azure 门户。

2. 导航到想要保护的 Azure 存储帐户的配置页面。 在“设置”页面中，选择“高级威胁防护”。

3. 在“高级威胁防护”配置边栏选项卡中
    * **打开**高级*威胁防护*
    * 单击“保存”以保存新的或更新的高级威胁防护策略。

![打开 Azure 存储高级威胁防护](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

## <a name="explore-anomalies"></a>探究异常情况

存储活动出现异常时，用户会收到一封电子邮件通知，其中包含有关可疑安全事件的信息。 事件详细信息包括：

* 异常情况的性质
* 存储帐户名称
* 存储类型
* 事件时间

电子邮件还包含有关可能原因的详细信息以及用于调查和缓解潜在威胁的建议操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

可从 Azure 安全中心的[安全警报磁贴](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)查看和管理当前安全警报。 单击特定警报可提供详细信息以及用于调查当前威胁和解决潜在威胁的操作。

![Azure 存储高级威胁防护警报电子邮件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>保护警报

警报是因访问或攻击存储帐户的异常且可能有害的尝试而生成的。 这些事件可以触发以下警报：

* **从异常位置访问**：当存储帐户的访问模式发生变化时，将触发此警报。 例如，当用户从异常地理位置访问存储帐户时。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员的维护操作）。 在其他情况下，警报会检测到恶意操作（前员工、外部攻击者等）。

* **异常数据提取**：当存储帐户的数据提取模式发生变化时，将触发此警报。 例如，当用户在存储帐户中访问的数据量异常时。 在某些情况下，警报会检测到合法操作（维护活动）。 在其他情况下，警报会检测到恶意操作（数据泄露、未经授权的数据传输）。

* **异常匿名访问**：当存储帐户的访问模式发生变化时，将触发此警报。 例如，当用户匿名访问存储帐户时。 在某些情况下，警报会检测到使用公共读取访问权限的合法访问。 在其他情况下，警报会检测到利用对容器及其 blob 的公共读取访问权限进行的未经授权访问。

* **意外删除**：根据对存储帐户的历史分析，当存储帐户中发生一个或多个意外删除操作时会触发此警报。 例如，假设用户使用新应用程序并通过新 IP 地址执行 *DeleteBlob* 操作。 在某些情况下，警报会检测到合法操作（管理员在出差时使用其他浏览器）。 在其他情况下，警报会检测到恶意操作（攻击者删除数据）。 
 
* **访问权限更改**：当存储帐户的访问权限发生意外更改时，将触发此警报。 例如，假设用户使用新应用程序并通过新 IP 地址更改对存储帐户的访问权限。 在某些情况下，警报会检测到合法操作（管理员在出差时使用其他浏览器）。 在其他情况下，警报会检测到恶意行为（例如，攻击者增加对已获得访问权限的帐户的权限）。 

* **上载 Azure 云服务包**：当 Azure 云服务包（*.cspkg* 文件）意外上载到存储帐户时，将触发此警报。 例如，假设从新 IP 地址上载了 *.cspkg* 文件。 在某些情况下，警报会检测到合法操作。 在其他情况下，警报会检测到恶意操作（例如，上载云服务包以准备部署恶意服务）。    
   

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 存储帐户日志](/rest/api/storageservices/About-Storage-Analytics-Logging)的详细信息

* 了解有关 [Azure 安全中心](../../security-center/security-center-intro.md)的详细信息