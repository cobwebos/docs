---
title: 使用 Microsoft Authenticator 应用（预览版）进行无密码 Azure AD 登录
description: 不使用密码通过 Microsoft Authenticator 应用（公共预览版）登录到 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247e772873cf467b371b4aea45f8ceb43bce16e3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180516"
---
# <a name="password-less-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>使用 Microsoft Authenticator 应用（公共预览版）进行无密码手机登录

使用 Microsoft Authenticator 应用可以登录到任何 Azure AD 帐户，且无需输入密码。 类似于 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 的技术，Microsoft Authenticator 使用基于密钥的身份验证来启用绑定到设备的用户凭据，并使用生物识别特征或 PIN。

![要求用户在其 Microsoft Authenticator 应用中批准登录尝试的浏览器登录示例](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

在 Microsoft Authenticator 应用中启用手机登录的用户在输入用户名后，看到的不是密码提示，而是一条消息，告知他们在应用中点击一个数字。 在应用中，该用户必须输入匹配的数字，选择“批准”，提供 PIN 或生物识别特征，然后身份验证将会完成。

## <a name="enable-my-users"></a>启用我的用户

在公共预览版中，管理员必须先通过 PowerShell 添加一个策略，以允许使用租户中的凭据。 在执行此步骤之前，请查看“已知问题”部分。

### <a name="tenant-prerequisites"></a>租户先决条件

* Azure Active Directory
* 为最终用户启用了 Azure 多重身份验证
* 用户可以注册其设备

### <a name="steps-to-enable"></a>启用步骤

1. 确保已安装 Azure Active Directory V2 PowerShell 模块的最新公共预览版。 你可能希望通过执行以下命令卸载并重新安装以确认这一点：
    ```powershell
    Uninstall-Module -Name AzureADPreview
    Install-Module -Name AzureADPreview
    ```

2. 向 Azure AD 租户进行身份验证以使用 Azure AD V2 PowerShell 模块。 所用帐户必须是安全管理员或全局管理员。
    ```powershell
    Connect-AzureAD
    ```

3. 创建 Authenticator 登录策略：
    ```powershell
    New-AzureADPolicy -Type AuthenticatorAppSignInPolicy -Definition '{"AuthenticatorAppSignInPolicy":{"Enabled":true}}' -isOrganizationDefault $true -DisplayName AuthenticatorAppSignIn
    ```

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>我的最终用户如何启用手机登录？

在公共预览版中，没有任何方法可以强制用户创建或使用此新凭据。 只有在管理员启用了最终用户的租户，并且用户已将其 Microsoft Authenticator 应用更新为启用手机登录时，最终用户才能使用无密码登录。

> [!NOTE]
> 此功能已在 2017 年 3 月开始植入到应用中，因此，在为租户启用该策略时，用户可能会立即遇到此流程。 请注意这一点，并让用户为此更改做好准备。
>

1. 在 Azure 多重身份验证中注册
1. 已在运行 iOS 8.0 或更高版本或者 Android 6.0 或更高版本的设备上安装最新版本的 Microsoft Authenticator。
1. 已在应用中添加支持推送通知的工作或学校帐户。 可在 [https://aka.ms/authappstart](https://aka.ms/authappstart) 中找到最终用户文档。

在 Microsoft Authenticator 应用中为用户设置支持推送通知的 MFA 帐户后，用户可以遵循[使用手机（而不是密码）登录](../user-help/microsoft-authenticator-app-phone-signin-faq.md)一文中的步骤完成手机登录注册。

## <a name="known-issues"></a>已知问题

### <a name="ad-fs-integration"></a>AD FS 集成

用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证始终默认为发送审批通知。 此逻辑可以防止将混合租户中的用户定向到 ADFS 进行登录验证，且无需用户执行附加的步骤来单击“改用密码”。 此过程还会绕过任何本地条件访问策略和直通身份验证流。 此过程的例外情况是，如果指定了 login_hint，则用户将自动定向到 AD FS，并且会绕过使用无密码凭据的选项。

### <a name="azure-mfa-server"></a>Azure MFA 服务器

通过组织的本地 Azure MFA 服务器启用了 MFA 的最终用户仍可以创建和使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个以上），则此项更改可能导致错误。  

### <a name="device-registration"></a>设备注册

创建这种新式强凭据的先决条件之一是，该凭据所在的设备已注册到单个用户的 Azure AD 租户中。 由于设备注册的限制，一个设备只能注册到一个租户中。 此项限制意味着，只能为 Microsoft Authenticator 应用中的一个工作或学校帐户启用手机登录。

## <a name="next-steps"></a>后续步骤

[了解设备注册](../devices/overview.md#getting-devices-under-the-control-of-azure-ad)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
