---
title: 查找 Azure 门户中的 Azure Active Directory 用户活动报告 | Microsoft Docs
description: 了解在 Azure 门户中何处查找 Azure Active Directory 用户活动报告。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: users-groups-roles
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f22219a0e2ff342e25a2efdeb319f389250ecfef
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231315"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>在 Azure 门户中查找活动报告

通过本文可以了解如何在 Azure 门户中查找 Azure Active Directory 用户活动报告。

## <a name="activity-and-integrated-app-reports"></a>活动和集成的应用报表

对于 Azure 门户中基于上下文的报告，现有报告将合并成单个视图。 单个基础 API 为视图提供数据。

若要查看此视图，请在“Azure Active Directory”边栏选项卡上的“活动”下，选择“审核日志”。

![审核日志](./media/active-directory-reporting-migration/482.png "审核日志")

以下报告合并在此视图中：

* 审核报表
* 密码重置活动
* 密码重置注册活动
* 自助服务组活动
* Office365 组名称更改
* 帐户设置活动
* 密码滚动更新状态
* 帐户设置错误


“应用程序使用情况”报告已增强并包含在“登录”视图中。 若要查看此视图，请在“Azure Active Directory”边栏选项卡上的“活动”下，选择“登录”。

![“登录”视图](./media/active-directory-reporting-migration/483.png "“登录”视图")

“登录”视图包含所有用户登录。可以使用此信息获取应用程序使用情况信息。 还可以在“管理”部分的**企业应用程序**概述中查看应用程序使用情况信息。

![企业应用程序](./media/active-directory-reporting-migration/484.png "企业应用程序")

## <a name="access-a-specific-report"></a>访问特定报告

虽然 Azure 门户提供了单个视图，但你也可以查看特定报告。

### <a name="audit-logs"></a>审核日志

为响应客户反馈，可以在 Azure 门户中使用高级筛选来访问所需的数据。 可以使用的一个筛选器是*活动类别*，其中列出了 Azure AD 中不同类型的活动日志。 要将结果的范围缩小到要查找的内容，可以选择某个类别。

例如，如果仅关注与自助服务密码重置相关的活动，可以选择“自助服务密码管理”类别。 可以看到的类别取决于正在处理的资源。  

![“筛选器审核日志”页上的类别选项](./media/active-directory-reporting-migration/06.png "“筛选器审核日志”页上的类别选项")

活动类别包括：

- 核心目录
- 自助服务密码管理
- 自助服务组管理
- 帐户预配

### <a name="application-usage"></a>应用程序使用情况

若要查看有关所有应用或单个应用的应用程序使用情况的详细信息，请在“活动”下选择“登录”。若要缩小结果范围，可以按用户名或应用程序名称进行筛选。

![“筛选登录事件”页](./media/active-directory-reporting-migration/07.png "“筛选登录事件”页")

### <a name="security-reports"></a>安全报表

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 异常活动报告

Azure AD 异常活动安全报告已合并，以向你提供一个中心视图。 此视图显示 Azure AD 可以检测和报告的所有与安全相关的风险事件。

下表列出了 Azure 门户中“Azure AD 异常活动”安全报告和相应的风险事件类型。

| Azure AD 异常活动报告 |  Identity Protection 风险事件类型|
| :--- | :--- |
| 具有已泄漏凭据的用户 | 已泄漏凭据 |
| 异常登录活动 | 不可能前往异常位置 |
| 从可能受感染的设备登录 | 从受感染的设备登录|
| 从未知源登录 | 从匿名 IP 地址登录 |
| 从具有可疑活动的 IP 地址登录 | 从具有可疑活动的 IP 地址登录 |
| - | 从不熟悉的位置登录 |

以下 Azure AD 异常活动安全报告未作为风险事件包含在 Azure 门户中：

* 多次失败后登录
* 从多个地理区域登录

有关详细信息，请参阅 [Azure Active Directory 风险事件](active-directory-identity-protection-risk-events.md)。  


#### <a name="detected-risk-events"></a>检测到的风险事件

在 Azure 门户中，可以在 **Azure Active Directory** 边栏选项卡的“安全性”部分下访问有关检测到的风险事件的报告。 可以在以下报告中跟踪检测到的风险事件：   

- 有风险的用户
- 有风险的登录

![安全报告](./media/active-directory-reporting-migration/04.png "安全报告")

有关安全报告的详细信息，请参阅：

- [Azure Active Directory 门户中的“有风险的用户”安全报告](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 门户中的“有风险的登录”报告](active-directory-reporting-security-risky-sign-ins.md)


要查看“应用程序使用情况”报告，请在“Azure Active Directory”边栏选项卡上的“管理”下，选择“企业应用程序”，并选择“登录”。


![企业应用程序登录报告](./media/active-directory-reporting-migration/199.png)

## <a name="next-steps"></a>后续步骤

有关报告的概述，请参阅 [Azure Active Directory 报告](active-directory-reporting-azure-portal.md)。
