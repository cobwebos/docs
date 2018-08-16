---
title: Azure AD Connect：无缝单一登录 - 常见问题 | Microsoft Docs
description: 提供了有关 Azure Active Directory 无缝单一登录常见问题的解答。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 29ed96044ceaa914db3f8b7090a1be5f65827e54
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627468"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 无缝单一登录：常见问题

本文解决了有关 Azure Active Directory 无缝单一登录（无缝 SSO）的常见问题。 请随时返回查看新内容。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>无缝 SSO 使用的登录方法是什么？

无缝 SSO 可与[密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)或[传递身份验证](active-directory-aadconnect-pass-through-authentication.md)登录方法结合使用。 但是，此功能不能用于 Active Directory 联合身份验证服务 (ADFS)。

## <a name="is-seamless-sso-a-free-feature"></a>无缝 SSO 是否为免费功能？

Seamless SSO 是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>能否在 [Microsoft Azure 德国云](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中使用无缝 SSO？

不是。 无缝 SSO 只能在全球范围内的 Azure AD 实例中使用。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>哪些应用程序可以利用无缝 SSO 的 `domain_hint` 或 `login_hint` 参数功能？

以下非详尽列表列出了可将这些参数发送到 Azure AD 的部分应用程序，因此使用无缝 SSO 可为用户提供无提示登录体验（即用户无需输入其用户名或密码）：

| 应用程序名称 | 可供使用的应用程序 URL |
| -- | -- |
| 访问面板 | https://myapps.microsoft.com/contoso.com |
| Outlook 网页版 | https://outlook.office365.com/contoso.com |
| Office 365 门户 | https://portal.office.com?domain_hint=contoso.com |

此外，如果应用程序向 Azure AD 的租用终结点（即 https://login.microsoftonline.com/contoso.com/<..> 或 https://login.microsoftonline.com/<tenant_ID>/<..>）而不是 Azure AD 的普通终结点（即 https://login.microsoftonline.com/common/<...>）发送登录请求，用户可获得无提示登录体验。 以下为提出此类登录请求的应用程序的非详尽列表。

| 应用程序名称 | 可供使用的应用程序 URL |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Azure 门户 | https://portal.azure.com/contoso.com |

在上述表中，将“contoso.com”替换为域名，为租户获取正确的应用程序 URL。

如果希望其他应用程序具备无提示登录体验，请在反馈部分告知我们。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>无缝 SSO 是否支持将 `Alternate ID` 作为用户名（而不是 `userPrincipalName`）？

是的。 在 Azure AD Connect 中进行配置时，无缝 SSO 支持将 `Alternate ID` 作为用户名，如[此处](active-directory-aadconnect-get-started-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅特定应用程序文档。

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>[Azure AD 加入](../active-directory-azureadjoin-overview.md) 与无缝 SSO 提供的单一登录体验有何不同？

如果用户的设备注册了 Azure AD，[Azure AD 加入](../active-directory-azureadjoin-overview.md)会向用户提供 SSO。 这些设备不必已加入域。 使用主刷新令牌或 PRT（而非 Kerberos）提供 SSO。 用户体验在 Windows 10 设备上最佳。 在 Edge 浏览器上，SSO 自动实现。 也可在 Chrome 上实现，但需要使用浏览器扩展插件。

可同时在租户上使用 Azure AD 加入和无缝 SSO。 这两种功能互补。 如果同时开启这两项功能，将先通过 Azure AD 加入实现 SSO，再进行无缝 SSO。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>我想要在 Azure AD 中注册非 Windows 10 设备，但不使用 AD FS。 是否可以改为使用无缝 SSO？

可以，此方案需要版本 2.1 或更高版本的[工作区加入客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>如何滚动更新 `AZUREADSSOACC` 计算机帐户的 Kerberos 解密密钥？

请务必在本地 AD 林中频繁滚动更新 `AZUREADSSOACC` 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。

>[!IMPORTANT]
>强烈建议至少每 30 天滚动更新一次 Kerberos 解密密钥。

在运行 Azure AD Connect 的本地服务器上执行以下步骤：

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步骤 1。 获取已在其中启用了无缝 SSO 的 AD 林列表

1. 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)。
3. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
4. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
5. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
6. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>步骤 2. 在设置 Kerberos 解密密钥的每个 AD 林上更新此密钥

1. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 AD 林的域管理员凭据。

    >[!NOTE]
    >我们使用以用户主体名称 (UPN) (johndoe@contoso.com) 格式或域限定的 SAM 帐户名（contoso\johndoe 或 contoso.com\johndoe）格式提供的域管理员用户名查找目标 AD 林。 如果你使用域限定的 SAM 帐户名，则我们使用用户名的域部分[使用 DNS 查找域管理员的域控制器](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)。 如果你使用的是 UPN，则我们在查找合适的域控制器前会[将它转换为域限定的 SAM 帐户名](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)。

2. 调用 `Update-AzureADSSOForest -OnPremCredentials $creds`。 此命令会在此特定 AD 林中更新 `AZUREADSSOACC` 计算机帐户的 Kerberos 解密密钥，并在 Azure AD 中对其进行更新。
3. 针对已设置了此功能的每个 AD 林重复上述步骤。

>[!IMPORTANT]
>确保运行 `Update-AzureADSSOForest` 命令_没有_超过一次。 否则，在用户的 Kerberos 票证过期并由本地 Active Directory 再次发布之前，此功能将停止运行。

## <a name="how-can-i-disable-seamless-sso"></a>如何禁用无缝 SSO？

### <a name="step-1-disable-the-feature-on-your-tenant"></a>步骤 1。 在租户上禁用此功能

#### <a name="option-a-disable-using-azure-ad-connect"></a>选项 A：使用 Azure AD Connect 进行禁用

1. 运行 Azure AD Connect，选择“更改用户登录页”，并单击“下一步”。
2. 取消选中“启用单一登录”选项。 在向导中继续操作。

完成向导后，将在租户上禁用无缝 SSO。 但是，屏幕上将显示以下消息：

“单一登录现已禁用，但可执行其他手动步骤以完成清理。 了解详细信息”

若要完成清理过程，请在运行 Azure AD Connect 的本地服务器上执行步骤 2 和 3。

#### <a name="option-b-disable-using-powershell"></a>选项 B：使用 PowerShell 进行禁用

在运行 Azure AD Connect 的本地服务器上运行以下步骤：

1. 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
4. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
5. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
6. 调用 `Enable-AzureADSSO -Enable $false`。

>[!IMPORTANT]
>使用 PowerShell 禁用无缝 SSO 不会更改 Azure AD Connect 中的状态。 无缝 SSO 在“更改用户登录”页面中将显示为已启用。

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步骤 2. 获取已在其中启用了无缝 SSO 的 AD 林列表

如果已使用 Azure AD Connect 禁用了无缝 SSO，请执行下面的步骤 1 到 5。 但是，如果是使用 PowerShell 禁用了无缝 SSO，请跳转到下面的步骤 6。

1. 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
4. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
5. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
6. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>步骤 3. 从列表中你所看到的每个 AD 林中手动删除 `AZUREADSSOACCT` 计算机帐户。

## <a name="next-steps"></a>后续步骤

- [快速入门](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [深入技术探究](active-directory-aadconnect-sso-how-it-works.md) - 了解此功能如何运作。
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
