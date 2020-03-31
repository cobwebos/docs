---
title: 调查 Azure 活动目录标识保护的风险
description: 了解如何在 Azure 活动目录标识保护中调查风险用户、检测和登录
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253476"
---
# <a name="how-to-investigate-risk"></a>如何：调查风险

身份保护为组织提供了三个报告，它们可用于调查其环境中的身份风险。 这些报告是**风险用户**、**风险登录**和**风险检测**。 调查事件是更好地了解和识别安全策略中的任何弱点的关键。

所有三个报告都允许在 中下载事件。用于 Azure 门户外部进一步分析的 CSV 格式。 风险用户和风险登录报告允许下载最近的 2500 个条目，而风险检测报告允许下载最近的 5000 条记录。

组织可以利用 Microsoft Graph API 集成将数据与组织中可能有权访问的其他源聚合。

这三个报告在**Azure 门户** > **Azure 活动目录** > **安全性**中找到。

## <a name="navigating-the-reports"></a>导航报表

每个报告都会在报告顶部显示的期间的所有检测的列表启动。 每个报表都允许根据管理员首选项添加或删除列。 管理员可以选择在 中下载数据。CSV 或 。JSON 格式。 可以使用报表顶部的筛选器筛选报表。

选择单个条目可能会启用报表顶部的其他条目，例如确认登录是否受损害或安全、确认用户已受损或消除用户风险的能力。

选择单个条目会扩展检测下方的详细信息窗口。 详细信息视图允许管理员调查并执行每个检测的操作。 

![显示危险登录和详细信息的标识保护报告示例](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>有风险用户

通过风险用户报告提供的信息，管理员可以找到：

- 哪些用户面临风险、已补救风险或已消除风险？
- 有关检测的详细信息
- 所有风险登录的历史
- 风险历史记录
 
然后，管理员可以选择对这些事件执行操作。 管理员可以选择：

- 重置用户密码
- 确认用户危害
- 消除用户风险
- 阻止用户登录
- 使用 Azure ATP 进一步调查

## <a name="risky-sign-ins"></a>有风险的登录

风险登录报告包含长达 30 天（1 个月）的可筛选数据。

通过风险登录报告提供的信息，管理员可以找到：

- 哪些登录被归类为有风险、已确认泄露、确认安全、已排除或修复。
- 与登录尝试关联的实时和聚合风险级别。
- 触发检测类型
- 应用条件访问策略
- MFA 详细信息
- 设备信息
- 应用程序信息
- 位置信息

然后，管理员可以选择对这些事件执行操作。 管理员可以选择：

- 确认登录折中方案
- 确认登录安全

## <a name="risk-detections"></a>风险检测

风险检测报告包含长达 90 天（3 个月）的可筛选数据。

通过风险检测报告提供的信息，管理员可以找到：

- 有关每个风险检测（包括类型）的信息。
- 同时触发的其他风险
- 登录尝试位置
- 链接到微软云应用安全 （MCAS） 的更多详细信息。

然后，管理员可以选择返回到用户的风险或登录报告，以便根据收集到的信息执行操作。

## <a name="next-steps"></a>后续步骤

- [可用于规避风险的策略](concept-identity-protection-policies.md)

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)
