---
title: 撤消 Azure Active Directory 的紧急用户访问权限 |Microsoft Docs
description: 在 Azure Active Directory 中的 Azure AD 管理中心批量添加用户
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 06/26/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5ca5f7c6032a69286da72d8ef3640f64038eb3a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027182"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>撤消 Azure Active Directory 中的用户访问权限

在可能需要管理员撤销用户对用户的所有访问权限的情况下，包括已泄露帐户、员工离职和其他内部威胁。 根据环境的复杂性，管理员可以执行几个步骤来确保访问被吊销。 在某些情况下，在访问吊销启动与访问被有效吊销之间可能会有一段时间。

若要缓解此风险，必须了解令牌的工作方式。 有多种类型的令牌，属于以下各节所述的模式之一。

## <a name="access-tokens-and-refresh-tokens"></a>访问令牌和刷新令牌

访问令牌和刷新令牌经常与胖客户端应用程序一起使用，并在基于浏览器的应用程序（例如单页面应用程序）中使用。

- 当用户对 Azure AD 进行身份验证时，将对授权策略进行评估，以确定是否可以向用户授予对特定资源的访问权限。  

- 如果获得授权，Azure AD 会颁发资源的访问令牌和刷新令牌。  

- 默认情况下，由 Azure AD 颁发的访问令牌默认为1小时。 如果身份验证协议允许，则当访问令牌过期时，应用程序可以通过将刷新令牌传递到 Azure AD 来自动对用户进行身份验证。

然后 Azure AD 重新评估其授权策略。 如果用户仍然获得授权，Azure AD 会颁发一个新的访问令牌和刷新令牌。

如果必须在短于令牌生存期的时间内撤销访问权限（通常在一小时内），访问令牌可能是一个安全问题。 出于此原因，Microsoft 正在积极地将[连续访问评估](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)引入到 Office 365 应用程序，这有助于确保访问令牌的不间断实时。  

## <a name="session-tokens-cookies"></a>会话令牌（cookie）

大多数基于浏览器的应用程序使用会话令牌，而不是访问和刷新令牌。  

- 当用户打开浏览器并通过 Azure AD 向应用程序进行身份验证时，用户将收到两个会话令牌。 一个来自 Azure AD，另一个应用程序。  

- 应用程序颁发其自己的会话令牌后，对应用程序的访问权限由应用程序的会话控制。 此时，用户仅受到应用程序识别的授权策略的影响。

- 当应用程序将用户回发到 Azure AD 时，将会重新计算 Azure AD 的授权策略。 重估通常会以静默方式发生，但频率取决于应用程序的配置方式。 只要会话令牌有效，应用就可能永远不会将用户发送回 Azure AD。

- 若要撤消会话令牌，应用程序必须基于其自身的授权策略吊销访问权限。 Azure AD 无法直接撤消应用程序颁发的会话令牌。  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>撤消对混合环境中用户的访问权限

对于包含本地 Active Directory 与 Azure Active Directory 同步的混合环境，Microsoft 建议 IT 管理员执行以下操作。  

### <a name="on-premises-active-directory-environment"></a>本地 Active Directory 环境

作为 Active Directory 中的管理员，请连接到本地网络，打开 PowerShell，并执行以下操作：

1. 禁用 Active Directory 中的用户。 请参阅[ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps)。

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. 在 Active Directory 中两次重置用户密码。 请参阅[set-adaccountpassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps)。

    > [!NOTE]
    > 更改用户密码两次的原因是为了降低哈希传递的风险，尤其是在本地密码复制延迟的情况下。 如果您可以安全地假设此帐户不安全，则您只能重置密码一次。

    > [!IMPORTANT] 
    > 不要在以下 cmdlet 中使用示例密码。 请确保将密码更改为随机字符串。

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory 环境

作为 Azure Active Directory 中的管理员，请打开 PowerShell，运行 ``Connect-AzureAD`` ，并执行以下操作：

1. 禁用 Azure AD 中的用户。 请参阅[get-azureaduser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0)。

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. 撤消用户的 Azure AD 刷新令牌。 请参阅[AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. 禁用用户的设备。 请参阅[get-azureaduserregistereddevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0)。

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>可选步骤

- [从 Intune 管理的应用程序中擦除公司数据](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe)。

- [擦除公司拥有的设备正在将设备重置为出厂默认设置](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe)。

> [!NOTE]
> 擦除后无法恢复设备上的数据。

## <a name="when-access-is-revoked"></a>撤消访问权限时

管理员完成上述步骤后，用户将无法获取与 Azure Active Directory 关联的任何应用程序的新令牌。 吊销和用户失去访问权限之间经过的时间取决于应用程序授予访问权限的方式：

- 对于**使用访问令牌的应用程序**，当访问令牌过期时，用户将失去访问权限。

- 对于**使用会话令牌的应用程序**，现有会话将在令牌过期后立即结束。 如果用户的禁用状态同步到了应用程序，则应用程序可以自动撤销用户的现有会话（如果配置为这样做）。  所花费的时间取决于应用程序和 Azure AD 之间的同步频率。

## <a name="next-steps"></a>后续步骤

- [Azure AD 管理员的安全访问实践](directory-admin-roles-secure.md)
- [添加或更新用户配置文件信息](../fundamentals/active-directory-users-profile-azure-portal.md)
