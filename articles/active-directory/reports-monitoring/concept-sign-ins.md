---
title: Azure Active Directory 门户中的“登录活动”报告 | Microsoft Docs
description: Azure Active Directory 门户中的“登录活动”报告简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246511"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“登录活动”报告

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** - 有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - [审核日志](concept-audit-logs.md)提供有关用户和组管理、托管应用程序和目录活动的系统活动信息。
- **安全性** 
    - **风险**登录 -[风险登录](concept-risky-sign-ins.md)是非用户帐户合法所有者的用户尝试登录的指示器。
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

本文概述了登录报告。

## <a name="prerequisites"></a>先决条件

### <a name="who-can-access-the-data"></a>谁可以访问该数据？

* 具有“安全管理员”、“安全读取者”、“全局读取者”和“报告读取者”角色的用户
* 全局管理员
* 任何用户（非管理员）都可以访问自己的登录活动 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>访问登录活动需要什么 Azure AD 许可证？

- 登录活动报告在所有[版本的 Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)中都有可用。

- 如果要使用 API 访问登录数据，租户必须具有与其关联的[Azure 活动目录高级](../fundamentals/active-directory-get-started-premium.md)许可证。



## <a name="sign-ins-report"></a>登录报告

用户登录报告提供了以下问题的答案：

* 什么是用户的登录模式？
* 多少用户超过一周都有登录行为？
* 这些登录的状态怎样？

在 [Azure 门户](https://portal.azure.com)菜单中，选择“Azure Active Directory”，或从任意页搜索并选择“Azure Active Directory”********。

![选择“Azure Active Directory”](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

在“监视”**** 下，选择“登录”**** 以打开[登录报告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)。

![登录活动](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "登录活动")

某些登录记录最多可能需要两个小时才会显示在门户中。

> [!IMPORTANT]
> 登录报告仅显示“交互式”登录，即用户使用其用户名和密码进行的手动登录****。 登录报告中不会显示服务到服务身份验证等非交互式登录。 

登录日志有一个默认列表视图，用于显示：

- 登录日期
- 相关的用户
- 用户登录到的应用程序
- 登录状态
- 风险检测的状态
- 多重身份验证 (MFA) 要求的状态

![登录活动](./media/concept-sign-ins/sign-in-activity.png "登录活动")

您可以通过单击工具栏中的**列**来自定义列表视图。

![登录活动](./media/concept-sign-ins/19.png "登录活动")

通过“列”**** 对话框，可以访问可选属性。 在登录报告中，对于给定的登录请求，不能将具有多个值的字段作为列。 例如，“身份验证详细信息”、“条件访问数据”和“网络位置”就是这样的。   

![登录活动](./media/concept-sign-ins/columns.png "登录活动")

选择列表视图中的某个项可获得更详细的信息。

![登录活动](./media/concept-sign-ins/basic-sign-in.png "登录活动")

> [!NOTE]
> 客户现在可以通过所有登录报告对条件访问策略进行故障排除。 通过单击登录记录的 **"条件访问**"选项卡，客户可以查看条件访问状态，并深入了解应用于登录的策略的详细信息以及每个策略的结果。
> 有关详细信息，请参阅[有关所有登录中 CA 信息的常见问题解答](reports-faq.md#conditional-access)。



## <a name="filter-sign-in-activities"></a>筛选登录活动

首先，将所报告数据的范围缩小到适当的级别。 接下来，使用充当默认筛选器的日期字段筛选登录数据。 Azure AD 为您提供了可以设置的各种附加筛选器：

![登录活动](./media/concept-sign-ins/04.png "登录活动")

**请求 ID** - 您关心的请求的 ID。

**用户**- 您关心的用户的名称或用户主体名称 （UPN）。

**应用程序**- 目标应用程序的名称。
 
**状态**- 您关心的登录状态：

- Success

- 失败

- 打断


**IP 地址**- 用于连接到租户的设备的 IP 地址。

**位置**- 连接从以下位置启动的位置：

- 城市

- 州/省

- 国家/地区


**资源**- 用于登录的服务的名称。


**资源 ID** - 用于登录的服务的 ID。


**客户端应用**- 用于连接到租户的客户端应用的类型：

![客户端应用筛选器](./media/concept-sign-ins/client-app-filter.png)


|“属性”|现代身份验证|描述|
|---|:-:|---|
|经过身份验证的 SMTP| |POP 和 IMAP 客户端用于发送电子邮件。|
|自动发现| |Outlook 和 EAS 客户端用于查找和连接到联机交换中的邮箱。|
|Exchange ActiveSync| |此筛选器显示尝试 EAS 协议的所有登录尝试。|
|浏览者|![勾选标记](./media/concept-sign-ins/check.png)|显示使用 Web 浏览器的用户的所有登录尝试|
|Exchange ActiveSync| | 使用 Exchange ActiceSync 显示使用客户端应用的用户连接到在线交换的所有登录尝试|
|Exchange Online PowerShell| |用于使用远程 PowerShell 连接到联机交换。 如果阻止 Exchange 在线 PowerShell 的基本身份验证，则需要使用 Exchange 在线 PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到交换在线 PowerShell。](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)|
|Exchange Web 服务| |Outlook、Mac Outlook 和第三方应用使用的编程界面。|
|IMAP4| |使用 IMAP 检索电子邮件的旧版邮件客户端。|
|通过 HTTP 的 MAPI| |由 Outlook 2010 及更高版本使用。|
|移动应用和桌面客户端|![勾选标记](./media/concept-sign-ins/check.png)|显示使用移动应用和桌面客户端的用户的所有登录尝试。|
|离线通讯簿| |Outlook 下载和使用的地址列表集合的副本。|
|随时随地进行展望（通过 HTTP 进行 RPC）| |由 Outlook 2016 及更早版本使用。|
|展望服务| |由 Windows 10 的邮件和日历应用使用。|
|POP3| |使用 POP3 检索电子邮件的旧版邮件客户端。|
|报告 Web 服务| |用于在"联机交换"中检索报表数据。|
|其他客户端| |显示未包含客户端应用或未知用户的所有登录尝试。|



**操作系统**- 设备上运行的操作系统使用登录到租户。 


**设备浏览器**- 如果连接是从浏览器启动的，则此字段允许您按浏览器名称进行筛选。


**关联 ID** - 活动的相关 ID。




**条件访问**- 应用的条件访问规则的状态

- **未应用**：在登录期间未应用于用户和应用程序的策略。

- **成功**：在登录期间应用于用户和应用程序（但不一定是其他条件）的一个或多个条件访问策略。 

- **失败**：应用了一个或多个条件访问策略，但在登录期间未满足。









## <a name="download-sign-in-activities"></a>下载登录活动

单击“下载”**** 选项即可创建包含最近 250,000 条记录的 CSV 或 JSON 文件。 如果想要在 Azure 门户外部使用登录活动数据，请先[下载登录数据](quickstart-download-sign-in-report.md)。  

![下载](./media/concept-sign-ins/71.png "下载")

> [!IMPORTANT]
> 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。  


## <a name="sign-ins-data-shortcuts"></a>登录数据快捷方式

Azure AD 和 Azure 门户都提供登录数据的其他入口点：

- 标识安全保护概述
- 用户
- 组
- 企业应用程序

### <a name="users-sign-ins-data-in-identity-security-protection"></a>标识安全保护中的用户登录数据

**标识安全保护**概述页中的用户登录图显示登录的每周聚合。时间段的默认值为 30 天。

![登录活动](./media/concept-sign-ins/06.png "登录活动")

单击登录图中的某一天时，可以获得该天的登录活动的概览。

登录活动列表中的每一行显示以下内容：

* 登录者是谁？
* 登录的目标应用程序是哪个？
* 登录的状态是什么？
* 登录的 MFA 状态是什么？

单击某个项即可获得有关登录操作的更多详情：

- 用户 ID
- 用户
- 用户名
- 应用程序 ID
- 应用程序
- 客户端
- 位置
- IP 地址
- Date
- 需要 MFA
- 登录状态

> [!NOTE]
> IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 从中心池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远，这会导致 IP 地址映射变得复杂。 目前，在 Azure AD 报告中，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。

在“用户”页中单击“活动”部分的“登录”即可完全了解所有用户登录活动。************

![登录活动](./media/concept-sign-ins/08.png "登录活动")

## <a name="usage-of-managed-applications"></a>托管应用程序的使用情况

通过登录数据的以应用程序为中心的视图，可以回答如下问题：

* 谁正在使用我的应用程序？
* 组织中最常用的三个应用程序是哪些？
* 我的最新应用程序的情况如何？

此数据的入口点是组织中最常用的三个应用程序。 数据包含在“企业应用程序”下“概览”部分过去 30 天的报告中********。

![登录活动](./media/concept-sign-ins/10.png "登录活动")

应用使用情况图显示指定时间内最常用的三个应用程序的按周汇总的登录信息。 默认时间为 30 天。

![登录活动](./media/concept-sign-ins/graph-chart.png "登录活动")

如果需要，可以将焦点设置在特定应用程序上。

![报告](./media/concept-sign-ins/single-app-usage-graph.png "报表")

单击应用程序使用情况图中的某一天时，可以获取登录活动的详细列表。

**登录** 选项可提供应用程序的所有登录事件的完整概览。

## <a name="office-365-activity-logs"></a>Office 365 活动日志

可以从 [Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)查看 Office 365 活动日志。 要考虑到 Office 365 活动和 Azure AD 活动日志共享大量的目录资源。 只有 Microsoft 365 管理中心提供 Office 365 活动日志的完整视图。 

还可以使用 [Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) 以编程方式访问 Office 365 活动日志。

## <a name="next-steps"></a>后续步骤

* [登录活动报告错误代码](reference-sign-ins-error-codes.md)
* [Azure AD 数据保留策略](reference-reports-data-retention.md)
* [Azure AD 报告延迟](reference-reports-latencies.md)

