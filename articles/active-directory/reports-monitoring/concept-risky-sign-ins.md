---
title: Azure Active Directory 门户中的“有风险的登录”报告 | Microsoft Docs
description: 了解 Azure Active Directory 门户中的“有风险的登录”报告
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629e7bf8ee7100b5927483ecfd4efa1d9223c151
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989920"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的“有风险的登录”报告

Azure Active Directory (Azure AD) 可以检测到与用户帐户相关的可疑操作。 每检测到一个可疑操作，就会创建一个名为“风险事件”的记录。 有关更多详细信息，请参阅 [Azure AD 风险事件](concept-risk-events.md)。 

你可以从 [Azure 门户](https://portal.azure.com)中通过选择“Azure Active Directory”边栏选项卡并导航到“安全性”部分来访问安全报告。 

有两个不同的安全报告，它们是根据风险事件计算的：

- **风险登录** - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

- **已标记为存在风险的用户** - 风险用户是指可能已泄露的用户帐户。 

![有风险的登录](./media/concept-risky-sign-ins/10.png)

若要了解如何配置触发这些风险事件的策略，请参阅[如何配置用户风险策略](../identity-protection/howto-user-risk-policy.md)。  

## <a name="who-can-access-the-risky-sign-ins-report"></a>谁可以访问风险登录报告？

风险登录报告可供以下角色的用户使用：

- 安全管理员
- 全局管理员
- 安全读取者

若要了解如何在 Azure Active Directory 中向用户分配管理角色，请参阅[在 Azure Active Directory 中查看和分配管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)。

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>访问安全报表需要什么 Azure AD 许可证？  

所有版本的 Azure AD 都提供风险登录报告。 但是，各版本的报表粒度级别有所不同： 

- 在 **Azure Active Directory 免费版和基本版**中，你将得到其中包含风险登录的列表。 

- 此外，**Azure Active Directory Premium 1** 版本还允许你检查每个报告中检测到的某些底层风险事件。 

- Azure Active Directory Premium 2 版本提供有关所有潜在风险事件的最详细信息，并且还允许配置可自动响应已配置风险级别的安全策略。

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Azure AD 免费版和基本版的风险登录报告

Azure AD 免费版和基本版提供针对用户检测到的有风险的登录列表。 每个记录包含以下属性：

- **用户** - 在登录操作期间使用的用户的名称。
- **IP** - 用于连接到 Azure Active Directory 的设备的 IP 地址。
- **位置** - 用于连接到 Azure Active Directory 的位置。 这是基于跟踪、注册表数据、反向查询和其他信息的最佳近似值。
- **登录时间** - 进行登录的时间
- **状态** - 登录的状态

![有风险的登录](./media/concept-risky-sign-ins/01.png)

根据你自己对风险登录的调查情况，可以通过采取以下操作向 Azure Active Directory 提供反馈：

- 解决
- 标记为误报
- 忽略
- 重新激活

![有风险的登录](./media/concept-risky-sign-ins/21.png)

此报告还提供用于执行以下操作的选项：

- 搜索资源
- 下载报告数据

![有风险的登录](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Azure Active Directory Premium 版的风险登录报告

Azure AD Premium 版的风险登录报告提供：

- 有关已检测到的[风险事件类型](concept-risk-events.md)的聚合信息。 使用 **Azure AD Premium P1 版本**时，许可证未涵盖的检测项会显示为风险事件“登录时检测到其他风险”。 使用 **Azure AD Premium P2 版本**时，可以获取有关所有基础检测的最详细的信息。

- 一个用于下载报表的选项

![有风险的登录](./media/concept-risky-sign-ins/456.png)

选择风险事件时，可获得此风险事件的详细报表视图，使你能够：

- 一个用于配置[用户风险补救策略](../identity-protection/howto-user-risk-policy.md)的选项  

- 查看风险事件的检测时间线  

- 查看已为其检测到此风险事件的用户的列表

- 手动关闭风险事件。 

![有风险的登录](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 有时，可能会在[登录报表](concept-sign-ins.md)中发现没有相应登录输入的风险事件。 这是因为“标识保护”评估“交互式”和“非交互式”登录的风险，而登录报表只显示交互式登录。

选择用户时，可获取此用户的详细报表视图，以便：

- 打开“所有的登录”视图

- 重置用户密码

- 清除所有事件

- 为用户调查报告的风险事件。 

![有风险的登录](./media/concept-risky-sign-ins/324.png)

若要调查风险事件，请从列表选择某个事件。  
这会打开此风险事件的“详细信息”边栏选项卡。 在“详细信息”边栏选项卡上，可选择手动关闭风险事件或重新激活已手动关闭的风险事件。 

![有风险的登录](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>后续步骤

- [如何配置用户风险策略](../identity-protection/howto-user-risk-policy.md)
- [如何配置风险补救策略](../identity-protection/howto-user-risk-policy.md)
- [风险事件类型](concept-risk-events.md)
