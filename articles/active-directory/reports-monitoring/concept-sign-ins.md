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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b69dca5abddd56b29abf3e482e51b3d2a41612e7
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864468"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“登录活动”报告

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** - 有关托管应用程序的使用情况和用户登录活动的信息。
    - **审核日志** - [审核日志](concept-audit-logs.md) - 有关用户和组管理、托管应用程序和目录活动的系统活动信息。
- **安全性** 
    - **风险登录** - [风险登录](concept-risky-sign-ins.md)是指可能由非用户帐户合法拥有者进行的登录尝试。
    - **已标记为存在风险的用户** - [风险用户](concept-user-at-risk.md)是指可能已泄露的用户帐户。

本主题概述了登录报告。

## <a name="prerequisites"></a>必备组件

### <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 具有“安全管理员”、“安全读取者”和“报告读取者”角色的用户
* 全局管理员
* 此外，任何用户（非管理员）都可以访问自己的登录活动 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>访问登录活动需要什么 Azure AD 许可证？
* 租户必须具有与之关联的 Azure AD Premium 许可证，才能查看包含所有登录活动的报告。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 请注意，如果在升级之前没有任何活动数据，则在升级到高级版许可证后，数据需要经过几天才会显示在报表中。

## <a name="sign-ins-report"></a>登录报告

用户登录报告提供了以下问题的答案：

* 什么是用户的登录模式？
* 多少用户超过一周都有登录行为？
* 这些登录的状态怎样？

可以通过在 [Azure 门户](https://portal.azure.com)的“Azure Active Directory”边栏选项卡的“活动”部分中选择“登录”来访问登录报告。 请注意，某些登录记录最多可能需要两个小时才会显示在门户中。

![登录活动](./media/concept-sign-ins/61.png "登录活动")

> [!IMPORTANT]
> 登录报告仅显示“交互式”登录，即用户使用其用户名和密码进行的手动登录。 登录报告中不会显示服务到服务身份验证等非交互式登录。 

登录日志有一个默认列表视图，用于显示：

- 登录日期
- 相关的用户
- 用户登录到的应用程序
- 登录状态
- 风险检测的状态
- 多重身份验证 (MFA) 要求的状态

![登录活动](./media/concept-sign-ins/01.png "登录活动")

单击工具栏中的“列”即可自定义列表视图。

![登录活动](./media/concept-sign-ins/19.png "登录活动")

用于显示其他字段，或者删除已显示的字段。

![登录活动](./media/concept-sign-ins/02.png "登录活动")

选择列表视图中的某个项可获得更详细的信息。

![登录活动](./media/concept-sign-ins/03.png "登录活动")

> [!NOTE]
> 客户现在可以通过所有登录报告对条件访问策略进行故障排除。 通过单击登录记录的“条件访问”选项卡，客户可以查看条件访问状态，并深入了解应用于登录的策略的详细信息以及每个策略的结果。
> 有关详细信息，请参阅[有关所有登录中 CA 信息的常见问题解答](reports-faq.md#conditional-access)。

![登录活动](./media/concept-sign-ins/ConditionalAccess.png "登录活动")


## <a name="filter-sign-in-activities"></a>筛选登录活动

若要将所报告数据的范围缩小到适当的级别，可以使用以下默认字段筛选登录数据：

- 用户
- 应用程序
- 登录状态
- 条件性访问
- 日期

![登录活动](./media/concept-sign-ins/04.png "登录活动")

“用户”筛选器用于指定所关注的用户的名称或用户主体名称 (UPN)。

“应用程序”筛选器用于指定所关注的应用程序的名称。

“登录状态”筛选器用于选择：

- All
- Success
- 失败

使用“条件访问”筛选器可以选择登录的 CA 策略状态：

- All
- 未应用
- Success
- 失败

“日期”筛选器用于定义已返回数据的时间范围。  
可能的值包括：

- 1 个月
- 7 天
- 24 小时
- 自定义时间范围

选择自定义时间范围时，可以配置开始时间和结束时间。

如果向登录视图添加其他字段，这些字段会自动添加到筛选器列表。 例如，如果向列表添加“客户端应用”字段，则还会获得另一筛选器选项，用于设置以下筛选器：  
![登录活动](./media/concept-sign-ins/12.png "登录活动")

- **浏览器**  
    此筛选器显示所有使用浏览器通信流执行的登录尝试的事件。
- **Exchange ActiveSync （支持）**  
    此筛选器将显示所有登录尝试其中的 Exchange ActiveSync (EAS) 协议已尝试从受支持的平台，如 iOS、 Android 和 Windows Phone。
- **Exchange ActiveSync （不支持）**  
    此筛选器将显示所有登录尝试其中 EAS 协议已尝试从等 Linux 发行版不受支持平台。
- **移动应用和桌面客户端**此筛选器显示没有使用浏览器通信流的所有登录尝试。 这可以是从使用任何协议的任何平台或桌面客户端应用程序，如 Windows 或 MacOS 上的 Office 移动应用。
  
- **其他客户端**
    - **IMAP**  
        使用 IMAP 检索电子邮件的旧邮件客户端。
    - **MAPI**  
        Office 2013，其中启用 ADAL 和它使用了 MAPI。
    - **旧式 Office 客户端**  
        Office 2013 中在未启用 ADAL 其默认配置，它使用 MAPI 或 Office 2016 ADAL 其中禁用了。
    - **POP**  
        使用 POP3 电子邮件中检索旧邮件客户端。
    - **SMTP**  
        使用 SMTP 发送电子邮件的旧邮件客户端。

## <a name="download-sign-in-activities"></a>下载登录活动

如果想要在 Azure 门户外部使用登录活动数据，可以[下载登录数据](quickstart-download-sign-in-report.md)。 单击**下载**为您提供创建 CSV 或 JSON 文件中的最新 250,000 记录的选项。  

![下载](./media/concept-sign-ins/71.png "下载")

> [!IMPORTANT]
> 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。  


## <a name="sign-ins-data-shortcuts"></a>登录数据快捷方式

除了 Azure AD 之外，Azure 门户也提供了登录数据的其他入口点：

- 标识安全保护概述
- 用户
- 组
- 企业应用程序

### <a name="users-sign-ins-data-in-identity-security-protection"></a>标识安全保护中的用户登录数据

用户在登录中的关系图**标识的安全保护**概述页显示在给定的时间段内按周汇总的所有用户的登录名。 默认时间为 30 天。

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
- Client
- 位置
- IP 地址
- 日期
- 需要 MFA
- 登录状态

> [!NOTE]
> IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 从中心池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远，这会导致 IP 地址映射变得复杂。 目前，在 Azure AD 报告中，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。

在“用户”页中单击“活动”部分的“登录”即可完全了解所有用户登录活动。

![登录活动](./media/concept-sign-ins/08.png "登录活动")

## <a name="usage-of-managed-applications"></a>托管应用程序的使用情况

通过登录数据的以应用程序为中心的视图，可以回答如下问题：

* 谁正在使用我的应用程序？
* 组织中最常用的 3 个应用程序是哪些？
* 我最近推出了一个应用程序。 它用起来怎样？

该数据的入口点为“概览”部分的“企业应用程序”下面的组织过去 30 天的报告中最常用的 3 个应用程序。

![登录活动](./media/concept-sign-ins/10.png "登录活动")

应用使用情况图按周汇总的登录为某个给定的时间段内前 3 个应用程序。 默认时间为 30 天。

![登录活动](./media/concept-sign-ins/47.png "登录活动")

如果需要，可以将焦点设置在特定应用程序上。

![报告](./media/concept-sign-ins/single_spp_usage_graph.png "报告")

单击应用程序使用情况图中的某一天时，可以获取登录活动的详细列表。

**登录** 选项可提供应用程序的所有登录事件的完整概览。

![登录活动](./media/concept-sign-ins/11.png "登录活动")

## <a name="office-365-activity-logs"></a>Office 365 活动日志

可以从 [Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)查看 Office 365 活动日志。 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，但只有 Microsoft 365 管理中心提供 Office 365 活动日志的完整视图。 

此外可以使用 [Office 365 管理 API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) 以编程方式访问 Office 365 活动日志。

## <a name="next-steps"></a>后续步骤

* [登录活动报告错误代码](reference-sign-ins-error-codes.md)
* [Azure AD 数据保留策略](reference-reports-data-retention.md)
* [Azure AD 报告延迟](reference-reports-latencies.md)

