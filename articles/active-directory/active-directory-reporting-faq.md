---
title: Azure Active Directory 报告常见问题 | Microsoft Docs
description: Azure Active Directory 报告常见问题。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d069d0e74c1bc10baa4d14cdb91c137203495ae2
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247402"
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory 报告常见问题

本文包括了对 Azure Active Directory (Azure AD) 报告常见问题的解答。 有关详细信息，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。 

## <a name="getting-started"></a>入门 

**问：我在使用 https://graph.windows.net/&lt;tenant-name&gt;/reports/ 终结点 API，以编程方式将 Azure AD 审核和集成的应用程序使用情况报告拉取到我们的报告系统中。我应当切换到什么？**

**答：** 请查看 [API 参考文档](https://developer.microsoft.com/graph/)，了解如何使用新的 API 访问[活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)。 此终结点有两个报告（审核和登录），它们提供了你在旧的 API 终结点中获取的所有数据。 此新的终结点还有一个登录报告，其中包含可用来获取应用使用情况、设备使用情况和用户登录信息的 Azure AD Premium 许可证。

--- 

**问：我在使用 https://graph.windows.net/&lt;tenant-name&gt;/reports/ 终结点 API，以编程方式将 Azure AD 安全报告（特定类型的检测，如泄漏的凭据或来自匿名 IP 地址的登录）拉取到我们的报告系统中。我应当切换到什么？**

**答：** 可以使用 [Identity Protection 风险事件 API](active-directory-identityprotection-graph-getting-started.md) 通过 Microsoft Graph 访问安全检测。 此新格式在如何查询数据方面提供了更大的灵活性，可以使用高级筛选、字段选择和其他手段；并且此新格式将风险事件标准化为一种类型，以便更轻松地集成到 SIEM 和其他数据收集工具中。 因为数据采用的格式不同，所以无法用新查询替代旧查询。 不过，[新 API 使用的是 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)，后者是 O365 或 Azure AD 之类的 API 的 Microsoft 标准。 因此，需要做的工作可以扩展当前 MS Graph 投资或者帮助开始向此新的标准平台进行转换。

--- 

**问：如何获得高级许可证？**

**答：** 请参阅 [Azure Active Directory Premium 入门](fundamentals/active-directory-get-started-premium.md)获取此问题的解答。

---

**问：获得高级许可证后多久能看见活动数据？**

**答：** 如果已有作为免费许可证的活动数据，则可以看到相同的数据。 如果没有任何数据，则将需要一天或两天。

---

**问：获得 Azure AD Premium 许可证后是否能查看上个月的数据？**

答：如果最近刚切换到高级版本（包括试用版），则最初最多能看到 7 天的数据。 随着数据累积，将最多可看到 30 天的数据。

---

**问：若要查看到 Azure 门户的活动登录或通过 API 获取数据，是否需要是全局管理员？**

**答：** 否。 必须是**安全读者**、**安全管理员**或**全局管理员**才能在 Azure 门户中或通过 API 获取报告数据。

---


## <a name="activity-logs"></a>活动日志


**问：Azure 门户中活动日志（审核和登录）的数据保留是什么？** 

**答：** 请参阅[收集的数据存储多久？](active-directory-reporting-retention.md#q-for-how-long-is-the-collected-data-stored)了解此问题的答案。

--- 

**问：完成任务后多久才能看到“活动”数据？**

**答：** 审核活动日志的延迟为 15 分钟到 1 小时。 对于某些记录，登录活动日志可能需要花费 15 分钟到多达 2 小时。

---


**问：是否可以通过 Azure 门户获取 Office 365 活动日志信息？**

**答：** 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，但如果需要 Office 365 活动日志的完整视图，应转到 Office 365 管理中心获取 Office 365 活动日志信息。

---


**问：应使用哪些 API 获取有关 Office 365 活动日志的信息？**

**答：** 可使用 Office 365 管理 API [通过一个 API 访问 Office 365 活动记录](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview)。

---

**问：可从 Azure 门户下载多少条记录？**

**答：** 最多可从 Azure 门户下载 5000 条记录。 记录按最近时间进行排序，默认情况下获取的是最近 5000 条记录。

---

**问：通过活动 API 可以查询多少条记录？**

**答：** 最多可查询 100 万条记录（不使用 top 运算符的情况，此运算符将记录按最近时间进行排序）。 如果使用“top”运算符，则最多可查询到 50 万条记录。 可在[此处](active-directory-reporting-api-getting-started.md)找到如何使用 API 的示例查询。

---

## <a name="risky-sign-ins"></a>有风险的登录

**问：标识保护存在风险事件，但未在所有登录中看到对应登录。** 这是正常情况吗？

**答：** 是的，标识保护会评估所有身份验证流的风险，无论其为交互式还是非交互式。 但是，所有登录报告仅显示交互式登录。

---

问：如何在 Azure 门户中下载“已标记为存在风险的用户”报告？

答：将尽快添加下载“已标记为存在风险的用户”报告的选项。

---

问：如何了解 Azure 门户中被标记为存在风险的用户或登录的原因？

答：Premium Edition 客户可以通过单击“已标记为存在风险的用户”中的用户或单击“有风险的登录”来了解潜在风险事件的详细信息。 免费和基本版客户可以看到有风险的用户和登录，但看不到潜在风险事件的信息。

---

**问：在登录和有风险的登录报告中，IP 地址是如何计算的？**

答：IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 出于某些因素这一点非常复杂，例如，从中央池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远。 鉴于上述原因，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。 

---

**问：风险事件“检测到具有附加风险的登录”指示什么？**

**答：** 为了让你深入了解环境中所有具有风险的登录，对于执行了 Azure AD Identity Protection 订阅者专用的检测登录，“登录时检测到其他风险”将充当其占位符。

---

## <a name="conditional-access"></a>条件性访问

**问：此功能有什么新内容？**

**答：** 客户现在可以通过所有登录报告对条件访问策略进行故障排除。 客户可以查看条件访问状态，并深入了解应用于登录的策略的详细信息以及每个策略的结果。

**问：如何开始使用？**

**答：** 若要开始使用：
    * 导航到 [Azure门户](https://portal.azure.com)中的登录报告。 
    * 单击要进行故障排除的登录。
    * 导航到“条件访问”选项卡。在这里，可以查看影响登录的所有策略以及每个策略的结果。 
    
**问：条件访问状态的所有可能值是什么？**

**答：** 条件访问状态可以具有以下值：
    * **未应用**：这表示在范围内没有针对用户和应用程序的 CA 策略。 
    * **成功**：这表示在范围内存在针对用户和应用程序的 CA 策略，并且已成功满足 CA 策略。 
    * **失败**：这表示在范围内存在针对用户和应用程序的 CA 策略，但不满足 CA 策略。 
    
**问：条件访问策略结果的所有可能值是什么？**

**答：** 条件访问策略可以具有以下结果：
    * **成功**：成功满足策略。
    * **失败**：不满足策略。
    * **未应用**：这可能是因为不符合策略条件。
    * **未启用**：这是由于策略处于禁用状态。 
    
**问：所有登录报告中的策略名称与 CA 中的策略名称不匹配，为什么？**

**答：** 所有登录报告中的策略名称均基于登录时的 CA 策略名称。 如果你后来（即登录后）更新了策略名称，则这可能与 CA 中的策略名称不一致。