---
title: 如何调查 Azure Active Directory Identity Protection 中的风险
description: 了解如何调查中有风险的用户、检测和登录 Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e04cadef1f6a4aee7d8f807fc7aaa999ba6da8d7
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887086"
---
# <a name="how-to-investigate-risk"></a>如何：调查风险

标识保护为组织提供了可用于在其环境中调查标识风险的三个报告。 这些报告包括有**风险的用户**、有风险的**登录**和**风险检测**。 调查事件对于更好地理解和识别安全策略中的任何薄弱点是关键。

所有三个报表都允许在中下载事件。CSV 格式，以便在 Azure 门户之外进行进一步分析。 有风险的用户和有风险的登录报表允许下载最新的2500条目，而风险检测报告允许下载最新的5000记录。

组织可以利用 Microsoft Graph 的 API 集成，将数据与可能有权访问作为组织的其他源聚合在一起。

这三个报表在 " **Azure 门户** > **Azure Active Directory** > **安全性**" 中找到。

## <a name="navigating-the-reports"></a>导航报表

每个报表都将使用报表顶部显示的期间的所有检测项列表来启动。 每个报表都允许根据管理员偏好添加或删除列。 管理员可以选择在中下载数据。CSV 格式。 使用报表顶部的筛选器可以筛选报表。

选择各个条目可能会在报表顶部启用附加条目，例如，能够确认登录为 "已泄露" 或 "安全"，确认用户是否被泄露或消除用户风险。

选择各个条目将展开检测下的详细信息窗口。 "详细信息视图" 允许管理员调查每个检测并对其执行操作。 

![显示有风险登录和详细信息的示例 Identity Protection 报表](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>有风险用户

使用有风险用户报告提供的信息，管理员可以找到：

- 哪些用户面临风险、已补救风险或已消除风险？
- 有关检测的详细信息
- 风险登录的历史记录
- 风险历史记录
 
然后，管理员可以选择对这些事件执行操作。 管理员可以选择：

- 重置用户密码
- 确认用户泄露
- 消除用户风险
- 阻止用户登录
- 使用 Azure ATP 进一步调查

## <a name="risky-sign-ins"></a>有风险的登录

"有风险的登录" 报告包含最多过去30天（1个月）的可筛选数据。

使用有风险的登录报告提供的信息，管理员可以找到：

- 哪些登录归类为风险、已确认泄露、已确认安全、已消除或已修正。
- 实时并聚合与登录尝试关联的风险级别。
- 触发的检测类型
- 已应用条件性访问策略
- MFA 详细信息
- 设备信息
- 应用程序信息
- 位置信息

然后，管理员可以选择对这些事件执行操作。 管理员可以选择：

- 确认登录泄露
- 确认登录安全

## <a name="risk-detections"></a>风险检测

风险检测报告包含最多过去90天（3个月）的可筛选数据。

使用风险检测报告提供的信息，管理员可以找到：

- 有关每个风险检测的信息，包括类型。
- 同时触发的其他风险
- 登录尝试位置
- 从 Microsoft Cloud App Security 链接到更多详细信息（MCAS）。

然后，管理员可以选择返回到用户的风险或登录报告，以根据收集的信息采取措施。

## <a name="next-steps"></a>后续步骤

- [可用于缓解风险的策略](concept-identity-protection-policies.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
