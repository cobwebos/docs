---
title: Privileged Identity Management 订阅 - Azure | Microsoft 文档
description: 说明在租户中管理和使用 Azure AD Privileged Identity Management 的订阅和许可要求
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 06/01/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b4322ab5c90b04cf4eb0591e97e5e026664a2769
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442366"
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Azure Active Directory Privileged Identity Management 订阅要求

Azure AD Privileged Identity Management 作为 Azure AD 的 Premium P2 版本的一部分提供。 有关 P2 的其他功能以及它与 Premium P1 比较如何的详细信息，请参阅 [Azure Active Directory 版本](../active-directory-editions.md)。

>[!NOTE]
当 Azure Active Directory (Azure AD) Privileged Identity Management 处于预览状态时，对试用此服务的租户不进行任何许可证检查。  现在 Azure AD Privileged Identity Management 已推出正式版，在 2016 年 12 月之后，租户中必须有试用版或付费版订阅才能继续使用 Privileged Identity Management。
  

## <a name="confirm-your-trial-or-paid-subscription"></a>确认试用版或付费版订阅

如果不确定组织是否有试用版订阅或已购买订阅，则可以使用 Windows PowerShell V1 的 Azure Active Directory 模块中包含的命令检查租户中是否存在订阅。 
1. 打开 PowerShell 窗口。
2. 输入 `Connect-MsolService` 作为租户中的用户进行身份验证。
3. 输入 `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`。

此命令可检索租户中的订阅列表。 如果未返回任何行，则需要获取 Azure AD Premium P2 试用版，购买 Azure AD Premium P2 订阅或 EMS E5 订阅，以便使用 Azure AD Privileged Identity Management。  若要获取试用版并开始使用 Azure AD Privileged Identity Management，请阅读 [Azure AD Privileged Identity Management 入门](../active-directory-privileged-identity-management-getting-started.md)。

如果此命令返回的行中 SkuPartNumber 为“AAD_PREMIUM_P2”或“EMSPREMIUM”且 IsTrial 为“True”，这指示 Azure AD Premium P2 试用版已存在于租户中。  如果未启用订阅状态，并且未购买 Azure AD Premium P2 或 EMS E5 订阅，则必须购买 Azure AD Premium P2 订阅或 EMS E5 订阅，才能继续使用 Azure AD Privileged Identity Management。

Azure AD Premium P2 通过 [Microsoft 企业协议](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)、[开放批量许可计划](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)和[云解决方案提供商计划](https://partner.microsoft.com/en-US/cloud-solution-provider)提供。 Azure 和 Office 365 订户还可以在线购买 Azure AD Premium P2。  有关 Azure AD Premium 定价以及如何在线订购的详细信息可以在 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)中找到。

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management 在租户中不可用

在以下情况下，Azure AD Privileged Identity Management 不再在租户中可用：
- 组织使用的是处于预览状态的 Azure AD Privileged Identity Management，并且未购买 Azure AD Premium P2 订阅或 EMS E5 订阅。
- 组织的 Azure AD Premium P2 或 EMS E5 试用版已过期。
- 组织购买的订阅已过期。

当 Azure AD Premium P2 订阅或 EMS E5 订阅过期，或者使用处于预览状态的 Azure AD Privileged Identity Management 的组织未获取 Azure AD Premium P2 或 EMS E5 订阅时：

- 对 Azure AD 角色的永久角色分配会受到影响。
- Azure 门户中的 Azure AD Privileged Identity Management 扩展，以及 Graph API cmdlet 和 Azure AD Privileged Identity Management 的 PowerShell 接口不再可供用户用来激活特权角色、管理特权访问或执行特权角色的访问评审。
- 将删除 Azure AD 角色的符合条件的角色分配，因为用户不再能够激活特权角色。
- Azure AD 角色的任何正在进行的访问评审将结束，并且将删除 Azure AD Privileged Identity Management 配置设置。
- 角色分配更改时，Azure AD Privileged Identity Management 不再发送电子邮件。

## <a name="next-steps"></a>后续步骤

- [Azure AD Privileged Identity Management 入门](../active-directory-privileged-identity-management-getting-started.md)
- [Azure AD Privileged Identity Management 中的角色](../active-directory-privileged-identity-management-roles.md)
