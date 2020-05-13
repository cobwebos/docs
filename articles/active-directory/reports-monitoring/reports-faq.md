---
title: Azure Active Directory 报告常见问题解答 | Microsoft Docs
description: 围绕 Azure Active Directory 报表的常见问题。
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 818528ae193209e23424998421ebe2fb0c2b24b3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199375"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>有关 Azure Active Directory 报告的常见问题解答

本文包括了对 Azure Active Directory (Azure AD) 报告常见问题的解答。 有关详细信息，请参阅 [Azure Active Directory 报告](overview-reports.md)。 

## <a name="getting-started"></a>入门 

**问：我目前使用 `https://graph.windows.net/<tenant-name>/reports/` 终结点 api 以编程方式将 Azure AD 审核和集成的应用程序使用情况报表纳入到报告系统中。我应该切换到哪些功能？**

**答：** 请查看 [API 参考](https://developer.microsoft.com/graph/)，了解如何[使用 API 访问活动报告](concept-reporting-api.md)。 此终结点有两个报告（**审核**和**登录**），其中提供了在旧 API 终结点中获取的所有数据。 此新的终结点还有一个登录报告，其中包含可用来获取应用使用情况、设备使用情况和用户登录信息的 Azure AD Premium 许可证。

---

**问：我目前使用 `https://graph.windows.net/<tenant-name>/reports/` 终结点 api 来以编程方式将 Azure AD 安全报告（如泄漏的凭据或来自匿名 IP 地址的登录）提取到我们的报告系统中。我应该切换到哪些功能？**

**答：** 你可以使用 [Identity Protection 风险检测 API](../identity-protection/graph-get-started.md)   通过 Microsoft Graph 访问安全检测。 这种新格式可让你更灵活地查询数据、高级筛选、字段选择等，并将风险检测标准化为一种类型，以便更轻松地集成到 Siem 和其他数据收集工具。 因为数据采用的格式不同，所以无法用新查询替代旧查询。 不过，[新 API 使用的是 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)，后者是 O365 或 Azure AD 之类的 API 的 Microsoft 标准。 因此，所需的工作可以扩展当前的 Microsoft Graph 投资，也可以帮助您开始过渡到这个新的标准平台。

---

**问：如何获得高级许可证？**

**答：** 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。

---

**问：获得高级许可证后多久能看见活动数据？**

**答：** 如果获得免费许可证时已有活动数据，则可以立即看到这些数据。 如果没有任何数据，则需要在一到两天后，数据才会显示在报告中。

---

**问：获得 Azure AD Premium 许可证后是否能查看上个月的数据？**

**答：** 如果最近切换到高级版本（包括试用版），则最多可查看7天内的数据。 随着数据累积，可以看到过去 30 天的数据。

---

**问：若要查看到 Azure 门户的活动登录或通过 API 获取数据，是否需要是全局管理员？**

**答：** 否。如果你是租户的**安全读取者**或**安全管理员**，也可以通过门户或 API 访问报告数据。 当然，**全局管理员**也有权访问这些数据。

---


## <a name="activity-logs"></a>活动日志


**问：Azure 门户中活动日志（审核和登录）的数据保留是什么？** 

**答：** 有关详细信息，请参阅[Azure AD 报表的数据保留策略](reference-reports-data-retention.md)。

---

**问：在完成任务后，我可以看到活动数据需要多长时间？**

**答：** 审核日志的延迟为 15 分钟到 1 小时。 对于某些记录，登录活动日志可能需要花费 15 分钟到 2 小时。

---

**问：是否可以通过 Azure 门户获取 Office 365 活动日志信息？**

**答：** 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，但如果需要 Office 365 活动日志的完整视图，则应转到[Microsoft 365 管理中心](https://admin.microsoft.com)以获取 Office 365 活动日志信息。

---

**问：应使用哪些 API 获取有关 Office 365 活动日志的信息？**

**答：** 使用[office 365 管理 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)通过 API 访问 Office 365 活动日志。

---

**问：可从 Azure 门户下载多少条记录？**

**答：** 最多可从 Azure 门户下载 5000 条记录。 记录按最近时间** 进行排序，默认情况下获取的是最近 5000 条记录。

---

## <a name="risky-sign-ins"></a>有风险的登录

**问：标识保护存在风险检测，但在 "登录" 报告中看不到相应的登录。这是不是吗？**

**答：** 是的，标识保护会评估所有身份验证流的风险，无论其为交互式还是非交互式。 但是，所有登录报告仅显示交互式登录。

---

问：如何了解 Azure 门户中被标记为存在风险的用户或登录的原因？****

**答：** 如果有**Azure AD Premium**订阅，可以通过在 "已**标记为风险的用户**" 中选择用户或在 "有**风险的登录**" 报表中选择一个记录来了解有关底层风险检测的详细信息。 如果你有**免费**订阅或**基本**订阅，则可以查看 "有风险的用户和有风险的登录" 报表，但看不到基础风险检测信息。

---

**问：在登录和有风险的登录报告中，IP 地址是如何计算的？**

答：**** IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 有多种因素会导致映射 IP 地址进一步变得复杂，例如，从中心池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远。 目前，在 Azure AD 报告中，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。 

---

**问： "检测到具有额外风险的登录" 的风险检测是什么意思？**

**答：** 为了让你深入了解环境中所有具有风险的登录，对于为了执行“Azure AD 标识保护”订阅方专用的检测而进行的登录，“登录时检测到其他风险”将充当占位符。

---

## <a name="conditional-access"></a>条件性访问

**问：此功能有什么新内容？**

**答：** 客户现在可以通过 "所有登录" 报告对条件访问策略进行故障排除。 客户可以查看条件访问状态，并深入了解应用于登录的策略的详细信息以及每个策略的结果。

**问：如何开始使用？**

**答：** 若要开始使用：

* 导航到 [Azure门户](https://portal.azure.com)中的登录报告。
* 单击要进行故障排除的登录。
* 导航到 "**条件性访问**" 选项卡。在此处，你可以查看所有受影响的登录策略和每个策略的结果。 
    
**问：条件访问状态的所有可能值有哪些？**

**答：** 条件访问状态可以具有以下值：

* **未应用**：这表示在范围内没有针对用户和应用程序的 CA 策略。 
* **成功**：这表示在范围内存在针对用户和应用程序的 CA 策略，并且已成功满足 CA 策略。 
* **失败**：这表示在范围内存在针对用户和应用程序的 CA 策略，但不满足 CA 策略。 
    
**问：条件访问策略结果的所有可能值有哪些？**

**答：** 条件性访问策略可以具有以下结果：

* **成功**：成功满足策略。
* **失败**：不满足策略。
* **未应用**：这可能是因为不符合策略条件。
* **未启用**：这是由于策略处于禁用状态。 
    
**问： "所有登录" 报表中的策略名称与 CA 中的策略名称不匹配。为什么?**

**答：** 所有登录报告中的策略名称均基于登录时的 CA 策略名称。 如果你后来（即登录后）更新了策略名称，则这可能与 CA 中的策略名称不一致。

**问：由于条件性访问策略，登录已被阻止，但登录活动报告显示登录成功。为什么?**

**答：** 当前，在应用条件访问时，登录报告不会显示 Exchange ActiveSync 方案的准确结果。 在某些情况下，当报表中的登录结果显示成功登录时，但由于条件性访问策略而导致登录实际失败。 
