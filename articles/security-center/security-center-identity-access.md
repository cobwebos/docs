---
title: 在 Azure 安全中心监视标识和访问 | Microsoft Docs
description: 了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 57e13f3ed619fa88f29397fe44b783bd3650f636
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759160"
---
# <a name="monitor-identity-and-access"></a>监视标识和访问

> [!TIP]
> 从 2020 年 3 月起，免费定价层上的所有订阅中都包含 Azure 安全中心的标识和访问建议。 如果你有订阅在免费层，则它们的安全功能分数将受到影响，因为它们之前未接受过标识和访问安全性评估。 

在安全中心识别出潜在的安全漏洞时，它会创建一些建议，指导完成配置所需控件以强化和保护资源的过程。

安全边界已从网络边界演进成标识边界。 安全性越来越少地与如何保护网络相关，而更多地与如何保护数据，以及如何管理应用和用户的安全性相关。 而现在，随着越来越多的数据和应用移到云中，标识成为新的外围。

监视标识活动即可在事件发生前主动采取措施，或事后采取针对性措施来阻止攻击尝试。 例如，安全中心可能会标记已弃用的帐户（不再需要且被 Azure Active Directory 阻止登录的帐户），以便删除。 

你可能会在 Azure 安全中心的“标识和访问资源安全性”部分中看到的建议示例包括：：

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 只多只为订阅指定 3 个所有者
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除弃用的帐户

有关这些建议的详细信息以及可能在此处看到的建议的完整列表，请参阅[标识和访问建议](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 如果订阅包含 600 多个帐户，则安全中心无法针对订阅运行标识建议。 未运行的建议列在“不可用的评估”下面。
安全中心无法针对云解决方案提供商 (CSP) 合作伙伴的管理代理运行标识建议。
>


“建议”页中的两种安全控制提供了所有标识和访问建议：

- 管理访问和权限 
- 启用 MFA

![包含与标识和访问相关的建议的两种安全控制](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>启用多重身份验证 (MFA)

启用 MFA 需要 [Azure Active Directory (AD) 租户权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。 

- 如果拥有高级版 AD，请使用[条件访问](../active-directory/conditional-access/concept-conditional-access-policy-common.md)启用 MFA。

- AD 免费版用户可按照 [AD 文档](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)中的说明在 Azure Active Directory 中启用安全默认设置，但仍会显示有关启用 MFA 的安全中心建议。


## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下文章：

- [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
- [在 Azure 安全中心保护 Azure SQL 服务和数据](security-center-sql-service-recommendations.md)