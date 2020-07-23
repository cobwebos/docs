---
title: 通过 Azure Active Directory 标识保护调查风险
description: 了解如何在 Azure Active Directory 标识保护中调查存在风险的用户、检测和登录
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3610fa5ea442e5ccb15255d8a1ddcee211871aeb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463937"
---
# <a name="how-to-investigate-risk"></a>如何：调查风险

标识保护服务为组织提供了三种可用于在其环境中调查标识风险的报表。 这些报表分别为“风险用户”、“风险登录”和“风险检测”  。 事件调查是深入了解和找出安全策略中任何弱点的关键。

这三种报表都能以 .CSV 格式下载事件，以供在 Azure 门户之外进一步分析。 风险用户和风险登录报表允许下载最近的 2500 个条目，而风险检测报表则允许下载最近的 5000 条记录。

组织可利用 Microsoft Graph API 集成，将数据与其可能有权作为组织访问的其他来源聚合在一起。

可通过“Azure 门户” > “Azure Active Directory” > “安全性”中找到这三个报表  。

## <a name="navigating-the-reports"></a>导航浏览报表

每个报表启动时，报表顶部都有一个列表来显示该时间段内的所有检测。 每个报表都允许根据管理员偏好添加或删除列。 管理员可选择以 .CSV 或 .JSON 格式下载数据。 使用报表顶部的筛选器可筛选报表。

选择单个条目时，可能会启用报表顶部的其他条目，例如确认登录是已泄露还是安全的功能、确认用户是否遭到入侵的功能，或消除用户风险的功能。

选择单个条目后，将展开检测下方的“详细信息”窗口。 管理员可通过“详细信息”视图对每次检测进行调查和执行操作。 

![显示了风险登录和详细信息的标识保护报表示例](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>有风险用户

通过风险用户报表提供的信息，管理员可找出：

- 哪些用户面临风险、其风险是否已得到修正，或其风险已被消除？
- 有关检测的详细信息
- 所有风险登录的历史记录
- 风险历史记录
 
然后，管理员可选择对这些事件执行操作。 管理员可选择：

- 重置用户密码
- 确认用户是否已遭入侵
- 消除用户风险
- 阻止用户登录
- 使用 Azure ATP 进一步调查

## <a name="risky-sign-ins"></a>有风险的登录

风险登录报表包含最多过去 30 天（1 个月）的可筛选数据。

通过风险登录报表提供的信息，管理员可找出：

- 哪些登录被归类为存在风险、确认已泄露、确认安全、已消除或已修正。
- 与登录尝试关联的实时风险级别和聚合风险级别。
- 触发的检测类型
- 应用的条件访问策略
- MFA 详细信息
- 设备信息
- 应用程序信息
- 位置信息

然后，管理员可选择对这些事件执行操作。 管理员可选择：

- 确认登录是否已泄露
- 确认登录是否安全

> [!NOTE] 
> 无论身份验证流是否是交互式的，标识保护服务都会评估所有身份验证流的风险。 但是，登录报表仅显示交互式登录。你可能会看到在非交互式登录时发生的风险登录，但该登录将不在 Azure AD 登录报表中显示。

## <a name="risk-detections"></a>风险检测

风险检测报表包含最多过去 90 天（3 个月）的可筛选数据。

通过风险检测报表提供的信息，管理员可找出：

- 每项风险检测的相关信息，包括类型。
- 同时触发的其他风险
- 尝试登录的位置
- 指向 Microsoft Cloud App Security (MCAS) 中更多详细信息的链接。

然后，管理员可选择返回到用户的风险或登录报表，根据收集到的信息采取措施。

## <a name="next-steps"></a>后续步骤

- [可用于规避风险的策略](concept-identity-protection-policies.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
