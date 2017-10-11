---
title: "监视 Operations Management Suite 安全和审核解决方案中的资源 | Microsoft 文档"
description: "本文档帮助你使用 OMS 安全和审核功能监视资源和标识安全问题。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>监视 Operations Management Suite 安全和审核解决方案中的资源
本文档帮助你使用 OMS 安全和审核功能监视资源和标识安全问题。

## <a name="what-is-oms"></a>什么是 OMS？
Microsoft Operations Management Suite (OMS) 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护本地和云基础结构。 有关 OMS 的详细信息，请参阅文章 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)。

## <a name="monitoring-resources"></a>监视资源
无论何时，都希望在第一时间阻止安全事件发生。 但是，不可能防止所有安全事件。 发生安全事件时，需要确保最大程度地降低其影响。  以下三个重要建议可用于最大程度地降低安全事件的数量和影响：

* 定期评估环境中的漏洞。
* 定期检查所有计算机系统和网络设备，确保它们已安装所有最新修补程序。
* 定期检查所有日志和日志记录机制，包括操作系统事件日志、应用程序特定日志和入侵检测系统日志。

OMS 安全和审核解决方案允许 IT 人员主动监视所有资源，这有助于最大程度地降低安全事件的影响。 OMS 安全和审核具有可用于监视资源的安全域。 安全域支持快速访问用于安全监视的选项，详细介绍了以下域：

* 恶意软件评估
* 更新评估
* 标识和访问

> [!NOTE]
> 有关所有这些选项的概述，请阅读 [Operations Management Suite 安全和审核解决方案入门](oms-security-getting-started.md)。
> 
> 

### <a name="monitoring-system-protection"></a>监视系统保护
在深度防御方法中，每个保护层对于资源的整体安全状态都至关重要。 检测到威胁的计算机和保护不足的计算机显示在“安全域”下的“恶意软件评估”磁贴中。 通过使用“恶意软件评估”上的信息，可标识计划，将保护应用于需要它的服务器。 若要访问此选项，请执行以下步骤：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
   
    ![安全和审核](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. 在“安全和审核”仪表板中，单击“安全域”下的“反恶意软件评估”。 “反恶意软件评估”仪表板显示，如下所示：

![恶意软件评估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

可使用“恶意软件评估”仪表板标识以下安全问题：

* **活动威胁**：已受到威胁并且系统中有活动威胁的计算机。
* **已修正的威胁：**已受到威胁但威胁已修正的计算机。
* **过期签名**：已启用恶意软件保护但签名过期的计算机。
* **无实时保护**：未安装反恶意软件的计算机。

### <a name="monitoring-updates"></a>监视更新
应用最新安全更新是安全最佳做法，它应包含在更新管理策略中。 Microsoft Monitoring Agent 服务 (HealthService.exe) 从监视的计算机读取更新信息，然后将此更新的信息发送到云中的 OMS 服务以供处理。 Microsoft Monitoring Agent 服务将配置为自动服务，应始终运行在目标计算机中。

![监视更新](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

逻辑应用于更新数据，云服务记录数据。 如果找到缺失的更新，它们会显示在“更新”仪表板上。 可以使用“**更新**”仪表板处理缺失的更新，并制订将其应用到需要这些更新的服务器的计划。 按照下面的步骤访问“更新”仪表板：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
2. 在“安全和审核”仪表板中，单击“安全域”下的“更新评估”。 “更新”仪表板显示，如下所示：

![更新评估](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

在此仪表板中，可以执行更新评估，了解计算机的当前状态并处理最严重的威胁。 通过使用“关键更新或安全更新”磁贴，IT 管理员能够访问有关缺少的更新的详细信息，如下所示：

![搜索结果](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

此报告包括可用于标识系统易遭受的威胁类型的重要信息，其中包括与安全更新相关联的 Microsoft KB 文章和具有更多漏洞详细信息的 MS 公告。

### <a name="monitoring-identity-and-access"></a>监视标识和访问
由于用户可从任意位置工作、使用不同设备并访问大量云应用和本地应用，因此务必保护其凭据。 凭据被盗攻击是指攻击者最初获取对普通用户凭据的访问权限，在网络内访问系统。 很多时候，此初始攻击仅是获取网络访问权限的一种方式，最终目标是发现特权帐户。 

攻击者将始终停留在网络中，使用任意可用工具从其他登录帐户的会话中提取凭据。 根据系统配置，这些凭据可以哈希、票证甚至是纯文本密码的形式提取。  

> [!NOTE]
> 直接公开到 Internet 的计算机将发现许多尝试使用所有类型的已知用户名（如管理员）登录的失败尝试。 在大多数情况下，如果未使用已知用户名并且密码强度足够，则此方法可行。
> 
> 

它可以在入侵者泄漏特权帐户之前标识他们。 可利用 **OMS 安全和审核解决方案**监视标识和访问。 按照下面的步骤访问“标识和访问”仪表板：

1. 在“Microsoft Operations Management Suite”主仪表板中，单击“安全和审核”磁贴。
2. 在“安全和审核”仪表板中，单击“安全域”下的“标识和访问”。 “标识和访问”仪表板显示，如下所示：

![标识和访问](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

作为常规监视策略的一部分，必须包括标识监视。 IT 管理员应查看是否存在具有多次尝试的特定有效用户名。 这可能表示获取了实际用户名并尝试暴力攻击的攻击者，或使用已过期的硬编码密码的自动工具。

此仪表板允许 IT 人员快速标识与公司资源的标识和访问相关的潜在威胁。 它对于标识潜在趋势也特别重要，例如在“登录超时”磁贴中，可以看见在一段时间内执行了多少次失败登录尝试。 在这种情况下，计算机 **FileServer** 收到了 35 次登录尝试。 单击此计算机可了解其更多详细信息。 

![更多详细信息](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

为此计算机生成的报告中包括有关此模式的重要详细信息。 注意到 **ACCOUNT** 列提供了用于尝试访问系统的用户帐户、**TIMEGENERATED** 列提供了尝试完成的时间间隔、**LOGONTYPENAME** 列提供了此尝试的完成位置。 如果这些尝试按程序在系统中本地执行，**PROCESS** 列会显示进程的名称。 在从程序尝试登录的方案中，已有可用的进程名称，现在可以在目标系统中执行进一步调查。

## <a name="see-also"></a>另请参阅
在本文档中，了解了如何使用 OMS 安全和审核解决方案监视资源。 若要了解有关 OMS 安全的详细信息，请参阅以下文章：

* [Operations Management Suite (OMS) 概述](operations-management-suite-overview.md)
* [Operations Management Suite 安全和审核解决方案入门](oms-security-getting-started.md)
* [监视和响应 Operations Management Suite 安全和审核解决方案中的安全警报](oms-security-responding-alerts.md)

