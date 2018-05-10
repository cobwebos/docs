---
title: 为所有 Azure 管理员启用 MFA
description: 全局管理员启用指南
ms.service: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: a7a08c54fe0c59e1e100e1c46e7a640da0692077
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>对订阅管理员实施多重身份验证 (MFA)

创建管理员（包括全局管理员帐户）时，使用强大的身份验证方法极为重要。

可以根据需要，通过为 IT 员工用户帐户分配特定管理员角色（例如 Exchange 管理员或密码管理员）来执行日常管理。
此外，对管理员启用 [Azure 多重身份验证 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)可提升用户登录和事务的安全层级。 Azure MFA 还可帮助 IT 部门减少使用透露的凭据访问企业数据的可能性。

例如：针对用户实施 Azure MFA，并将其配置为使用电话呼叫或短信作为验证方法。 如果用户的凭据被透露，攻击者无法访问任何资源，因为攻击者无权访问用户的电话。 未添加额外标识保护层的组织将更容易受到凭据窃取攻击，从而导致数据泄漏。

想要保留完整本地身份验证控制权的组织可使用替代方法：使用 [Azure 多重身份验证服务器](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server)（也称为“本地 MFA”）。 使用此方法仍可实施多重身份验证，同时本地保留 MFA 服务器。

若要查看组织中具有管理权限的人员，可使用如下 Microsoft Azure AD V2 PowerShell 命令进行确证：

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>启用 MFA

请先查看 [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) 运行原理，然后继续。

只要用户的许可证包含 Azure 多重身份验证，则不需执行任何操作来启用 Azure MFA。 可以在单个用户的基础上，开始要求进行双重验证。 启用 Azure MFA 的许可证是：

- Azure 多重身份验证
- Azure Active Directory Premium
- 企业移动性 + 安全性

## <a name="turn-on-two-step-verification-for-users"></a>为用户开启双重验证

执行[如何要求对用户或组进行双重验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)中列出的某个过程，开始使用 Azure MFA。 可以选择强制要求所有登录都进行双重验证，也可以创建条件性访问策略，仅在事关重大时要求双重验证。

