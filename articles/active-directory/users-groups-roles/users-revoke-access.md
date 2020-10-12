---
title: 在 Azure Active Directory 中紧急撤消用户访问权限 | Microsoft Docs
description: 如何撤消 Azure Active Directory 中用户的所有访问权限
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 07/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8d1b6d912cadb073251d72ee327f4b19950ab1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055179"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>在 Azure Active Directory 中撤销用户访问

可能需要管理员撤销用户的所有访问权限的情况包括已泄露帐户、员工离职和其他内部威胁。 根据环境的复杂性，管理员可以采取几个步骤来确保访问被撤销。 在某些情况下，从开始撤销访问到有效撤销访问之间可能有一段时间。

若要缓解此风险，必须了解令牌的工作原理。 令牌有很多种类型，属于以下各节所述的模式之一。

## <a name="access-tokens-and-refresh-tokens"></a>访问令牌和刷新令牌

访问令牌和刷新令牌经常用于胖客户端应用程序，也用于基于浏览器的应用程序（例如单页应用）。

- 当用户对 Azure AD 进行身份验证时，将评估授权策略，以确定是否可以授予用户访问特定资源的权限。  

- 如果获得授权，Azure AD 将为资源颁发访问令牌和刷新令牌。  

- 默认情况下，Azure AD 颁发的访问令牌持续 1 小时。 如果身份验证协议允许，当访问令牌过期时，应用程序可以通过将刷新令牌传递给 Azure AD 来无提示地重新对用户进行身份验证。

然后 Azure AD 重新评估其授权策略。 如果用户仍获得授权，则 Azure AD 将发布新的访问令牌和刷新令牌。

如果必须在短于令牌的生存期（通常为一小时左右）内撤销访问，则访问令牌可能是一个安全问题。 出于这个原因，Microsoft 正在积极努力将[连续访问评估](../conditional-access/concept-continuous-access-evaluation.md)引入 Microsoft 365 应用程序，这有助于确保访问令牌以近实时方式失效。  

## <a name="session-tokens-cookies"></a>会话令牌 (cookies)

大多数基于浏览器的应用程序使用会话令牌，而不是访问和刷新令牌。  

- 当用户打开浏览器并通过 Azure AD 对应用程序进行身份验证时，用户将收到两个会话令牌。 一个来自 Azure AD，另一个来自应用程序。  

- 应用程序发出自己的会话令牌后，对该应用程序的访问将由应用程序的会话控制。 此时，用户只受应用程序能够识别的授权策略的影响。

- 当应用程序将用户发送回 Azure AD 时，会经常重新评估 Azure AD 的授权策略。 重估通常会以无提示方式发生，但频率取决于应用程序的配置方式。 只要会话令牌有效，应用程序可能永远不会将用户发送回 Azure AD。

- 对于要撤销的会话令牌，应用程序必须基于其自身的授权策略来撤消访问。 Azure AD 无法直接撤销应用程序颁发的会话令牌。  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>撤消对混合环境中用户的访问权限

对于使用本地 Active Directory 与 Azure Active Directory 同步的混合环境，Microsoft 建议 IT 管理员采取以下操作。  

### <a name="on-premises-active-directory-environment"></a>本地 Active Directory 环境

作为 Active Directory 中的管理员，请连接到本地网络，打开 PowerShell，然后执行以下操作：

1. 禁用 Active Directory 中的用户。 请参阅 [Disable-ADAccount](/powershell/module/addsadministration/disable-adaccount?view=win10-ps)。

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. 在 Active Directory 中重置用户密码两次。 请参阅 [Set-ADAccountPassword](/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps)。

    > [!NOTE]
    > 更改用户密码两次的原因是为了降低传递哈希的风险，尤其是在本地密码复制出现延迟的情况下。 如果你可以安全地假设此帐户没有遭到泄露，则只能重置密码一次。

    > [!IMPORTANT] 
    > 不要在以下 cmdlet 中使用示例密码。 请确保将密码更改为随机字符串。

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory 环境

作为 Azure Active Directory 中的管理员，打开 PowerShell，运行 ``Connect-AzureAD``，然后执行以下操作：

1. 禁用 Azure AD 中的用户。 请参阅 [Set-AzureADUser](/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0)。

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. 撤消用户的 Azure AD 刷新令牌。 请参阅 [Revoke-AzureADUserAllRefreshToken](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. 禁用用户的设备。 请参阅 [Get-AzureADUserRegisteredDevice](/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0)。

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>可选步骤

- [从 Intune 托管应用中擦除公司数据](/mem/intune/apps/apps-selective-wipe)。

- [擦除公司拥有的设备将设备重置为出厂默认设置](/mem/intune/remote-actions/devices-wipe)。

> [!NOTE]
> 擦除后无法恢复设备上的数据。

## <a name="when-access-is-revoked"></a>访问已撤消时

管理员完成上述步骤后，用户将无法为任何绑定到 Azure Active Directory 的应用程序获得新的令牌。 吊销和用户失去访问权限之间的时间取决于应用程序授予访问权限的方式：

- 对于使用访问令牌的应用程序，当访问令牌过期时，用户将失去访问权限。

- 对于使用会话令牌的应用程序，现有会话在令牌过期后立即结束。 如果用户的禁用状态与应用程序同步，则应用程序可以自动撤销用户的现有会话（如果配置为这样做的话）。  所需时间取决于应用程序和 Azure AD 之间的同步频率。

## <a name="next-steps"></a>后续步骤

- [Azure AD 管理员的安全访问实践](directory-admin-roles-secure.md)
- [添加或更新用户配置文件信息](../fundamentals/active-directory-users-profile-azure-portal.md)