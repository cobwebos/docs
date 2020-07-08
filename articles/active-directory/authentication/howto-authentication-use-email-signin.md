---
title: 使用电子邮件作为 Azure Active Directory 的备用登录 ID 登录
description: 了解如何配置并允许用户使用其电子邮件地址作为备用登录 ID 登录到 Azure Active Directory（预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: 0a7048e79ddd4a86d7e14e573cf5b8556f462f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550329"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>使用电子邮件作为备用登录 ID （预览版）登录到 Azure Active Directory

许多组织希望让用户使用与其本地目录环境相同的凭据登录到 Azure Active Directory （Azure AD）。 使用此方法（称为混合身份验证），用户只需记住一组凭据。

一些组织尚未转换为使用混合身份验证，原因如下：

* 默认情况下，Azure AD 用户主体名称（UPN）设置为与本地目录相同的 UPN。
* 更改 Azure AD UPN 将在本地和 Azure AD 环境之间创建错误匹配，这可能会导致某些应用程序和服务出现问题。
* 由于业务或合规性原因，组织不希望使用本地 UPN 登录到 Azure AD。

若要帮助移动到混合身份验证，你现在可以将 Azure AD 配置为允许用户使用已验证域中的电子邮件作为备用登录 ID 进行登录。 例如，如果 Contoso 更名为 Fabrikam，现在就可以不再使用旧 `balas@contoso.com` UPN 登录，而是可以使用电子邮件作为备用登录 ID 。 若要访问应用程序或服务，用户可以使用分配的电子邮件（例如）登录到 Azure AD `balas@fabrikam.com` 。

> [!NOTE]
> 使用电子邮件作为备用登录 ID 登录到 Azure AD 是 Azure Active Directory 的一项公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD 登录方法概述

若要登录到 Azure AD，用户请输入唯一标识其帐户的名称。 以前，只能使用 Azure AD UPN 作为登录名。

对于本地 UPN 是用户首选登录电子邮件的组织而言，此方法非常有用。 这些组织会将 Azure AD UPN 设置为与本地 UPN 完全相同的值，并且用户具有一致的登录体验。

但是，在某些组织中，本地 UPN 不用作登录名。 在本地环境中，可以将本地 AD DS 配置为允许使用备用登录 ID 进行登录。 将 Azure AD UPN 设置为与本地 UPN 相同的值并不是一个选项，因为 Azure AD 会要求用户使用该值登录。

此问题的典型解决方法是将 Azure AD UPN 设置为用户希望登录时所用的电子邮件地址。 虽然这种方法有效，但在本地 AD 与 Azure AD 中的 Upn 不同，并且此配置与所有 Microsoft 365 工作负荷都不兼容。

另一种方法是将 Azure AD 和本地 Upn 同步到相同的值，然后将 Azure AD 配置为允许用户使用已验证的电子邮件登录到 Azure AD。 若要提供此功能，请在本地目录中的用户*ProxyAddresses*属性中定义一个或多个电子邮件地址。 然后，将*ProxyAddresses*同步到使用 Azure AD Connect 自动 Azure AD。

## <a name="preview-limitations"></a>预览版限制

在当前预览状态下，当用户使用非 UPN 电子邮件作为备用登录 ID 登录时，会出现以下限制：

* 用户可能会看到其 UPN，即使是通过其非 UPN 电子邮件登录的。 可能会出现以下示例行为：
    * 当定向到 Azure AD 登录时，系统将提示用户登录 UPN `login_hint=<non-UPN email>` 。
    * 当用户使用非 UPN 电子邮件登录并输入错误密码时， *"输入密码"* 页将更改以显示 UPN。
    * 在某些 Microsoft 网站和应用（例如 [https://portal.azure.com](https://portal.azure.com) 和 Microsoft Office）上，通常显示在右上角的 "**帐户管理器**" 控件可能会显示用户的 UPN，而不是用于登录的非 UPN 电子邮件。

* 某些流当前与非 UPN 电子邮件不兼容，如下所示：
    * 标识保护当前不匹配电子邮件备用登录 Id，其中包含*泄漏的凭据*风险检测。 此风险检测使用 UPN 来匹配已泄漏的凭据。 有关详细信息，请参阅[Azure AD Identity Protection 风险检测和修正][identity-protection]。
    * 不完全支持将 B2B 邀请发送到备用登录 ID 的电子邮件。 接受作为备用登录 ID 发送到电子邮件的邀请后，使用备用电子邮件登录可能对租户终结点上的用户不起作用。

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>将登录电子邮件地址同步到 Azure AD

传统 Active Directory 域服务 (AD DS) 或 Active Directory 联合身份验证服务 (AD FS) 的身份验证直接在网络上进行，并且由 AD DS 基础结构处理。 通过混合身份验证，用户可以改为直接登录到 Azure AD。

若要支持这种混合身份验证方法，请使用 [Azure AD Connect][azure-ad-connect] 将本地 AD DS 环境同步到 Azure AD，并将其配置为使用密码哈希同步 (PHS) 或传递身份验证 (PTA)。

在这两个配置选项中，用户将其用户名和密码提交到 Azure AD，Azure AD 验证凭据并颁发票证。 当用户登录到 Azure AD 时，组织无需再托管和管理 AD FS 基础结构。

![使用密码哈希同步的 Azure AD 混合标识图表](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![使用传递身份验证的 Azure AD 混合标识图表](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect 自动同步的用户属性之一是 ProxyAddresses。 如果用户在本地 AD DS 环境中定义了电子邮件地址，且该地址是 ProxyAddresses 属性的一部分，则该地址会自动同步到 Azure AD。 然后，可以将此电子邮件地址作为备用登录 ID 直接用于 Azure AD 登录过程。

> [!IMPORTANT]
> 仅租户的已验证域中的电子邮件会同步到 Azure AD。 每个 Azure AD 租户都有一个或多个已验证的域，你对这些域具有已验证的所有权，并且这些域唯一绑定到你的租户。
>
> 有关详细信息，请参阅[在 Azure AD 中添加和验证自定义域名][verify-domain]。

有关详细信息，请参阅[为 Azure AD 混合标识解决方案选择正确的身份验证方法][hybrid-auth-methods]。

## <a name="enable-user-sign-in-with-an-email-address"></a>允许用户使用电子邮件地址登录

使用 Azure AD Connect 将应用了 ProxyAddresses 属性的用户同步到 Azure AD 之后，你需要启用该功能，以便用户能够使用电子邮件作为租户的备用登录 ID 进行登录。 此功能告知 Azure AD 登录服务器不仅要对照 UPN 值检查登录名，还要对照电子邮件地址的 ProxyAddresses 值进行检查。

在预览期间，当前只能使用 PowerShell 来启用使用电子邮件作为备用登录 ID 进行登录这一功能。 需要使用租户管理员权限完成以下步骤：

1. 以管理员身份打开 PowerShell 会话，然后使用 [Install-Module][Install-Module] cmdlet 安装 AzureADPreview 模块：

    ```powershell
    Install-Module AzureADPreview
    ```

    如果出现提示，选择“Y”以安装 NuGet 或从不受信任的存储库进行安装。

1. 使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 以租户管理员身份登录到 Azure AD 租户：

    ```powershell
    Connect-AzureAD
    ```

    该命令将返回有关你的帐户、环境和租户 ID 的信息。

1. 使用如下所示的 [Get-AzureADPolicy][Get-AzureADPolicy] cmdlet 检查租户中是否已存在 HomeRealmDiscoveryPolicy 策略：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. 如果当前未配置任何策略，则该命令将不返回任何内容。 如果返回策略，请跳过此步骤，并转到下一步以更新现有策略。

    若要将 HomeRealmDiscoveryPolicy 策略添加到租户，请使用 [New-AzureADPolicy][New-AzureADPolicy] cmdlet，并将 AlternateIdLogin 属性设置为“"已启用": true”，如以下示例所示  ：

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    成功创建策略后，该命令将返回策略 ID，如以下示例输出所示：

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 如果已有配置的策略，请检查是否已启用 AlternateIdLogin  属性，如以下示例策略输出所示 ** ：

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    如果策略存在但 AlternateIdLogin 属性不存在或未启用，或者要保留的策略中存在其他属性，请使用 [Set-AzureADPolicy][Set-AzureADPolicy] cmdlet 更新现有策略。

    > [!IMPORTANT]
    > 更新策略时，请确保已包含任何旧设置和新 AlternateIdLogin 属性 ** 。

    以下示例添加了 AlternateIdLogin 属性，并保留了可能已设置的 AllowCloudPasswordValidation 属性 **  ** ：

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    确认更新后的策略显示了所做的更改且现已启用 AlternateIdLogin 属性：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>测试用户是否能够通过电子邮件登录

若要测试用户是否能够使用电子邮件登录，请浏览到 [https://myprofile.microsoft.com][my-profile] 并使用基于其电子邮件地址（例如 `balas@fabrikam.com` ）而不是其 UPN（例如 `balas@contoso.com` ）的用户帐户登录。 登录体验应类似于基于 UPN 的登录事件。

## <a name="troubleshoot"></a>疑难解答

如果用户在使用其电子邮件地址进行登录时遇到问题，请查看以下故障排除步骤：

1. 确保用户帐户已为本地 AD DS 环境中的 ProxyAddresses 属性设置了电子邮件地址。
1. 验证 Azure AD Connect 是否已配置并成功将用户帐户从本地 AD DS 环境同步到 Azure AD。
1. 确认 Azure AD HomeRealmDiscoveryPolicy 策略已将 AlternateIdLogin 属性设置为 "已启用": true  ：

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>后续步骤

若要了解有关混合标识（例如 Azure AD 应用代理或 Azure AD 域服务）的详细信息，请参阅[用于访问和管理本地工作负载的 Azure AD 混合标识][hybrid-overview]。

有关混合标识操作的详细信息，请参阅[密码哈希同步][phs-overview]或[传递身份验证][pta-overview]同步的工作原理。

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
