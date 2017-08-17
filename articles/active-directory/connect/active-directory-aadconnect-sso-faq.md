---
title: "Azure AD Connect：无缝单一登录 - 常见问题 | Microsoft Docs"
description: "提供了有关 Azure Active Directory 无缝单一登录常见问题的解答。"
services: active-directory
keywords: "什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 518b2719f24be96dffba3458f6c15e65f16b7e0d
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 无缝单一登录：常见问题

本文解决了有关 Azure Active Directory 无缝单一登录（无缝 SSO）的常见问题。 请随时返回查看新内容。

>[!IMPORTANT]
>无缝 SSO 功能目前处于预览状态。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>无缝 SSO 使用的登录方法是什么？

无缝 SSO 可与[密码哈希同步](active-directory-aadconnectsync-implement-password-synchronization.md)或[传递身份验证](active-directory-aadconnect-pass-through-authentication.md)登录方法结合使用。 但是，此功能不能用于 Active Directory 联合身份验证服务 (ADFS)。

## <a name="is-seamless-sso-a-free-feature"></a>无缝 SSO 是否为免费功能？

Seamless SSO 是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。 功能正式发布时仍然可以免费使用。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>哪些应用程序可以利用无缝 SSO 的 `domain_hint` 或 `login_hint` 参数功能？

我们正在编译能够/无法发送这些参数的应用程序列表。 如果你有感兴趣的应用程序，请在备注部分告诉我们。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>无缝 SSO 是否支持将 `Alternate ID` 作为用户名（而不是 `userPrincipalName`）？

是的。 在 Azure AD Connect 中进行配置时，无缝 SSO 支持将 `Alternate ID` 作为用户名，如[此处](active-directory-aadconnect-get-started-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅具体应用程序文档。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>我想要在 Azure AD 中注册非 Windows 10 设备，但不使用 AD FS。 是否可以改为使用无缝 SSO？

可以，此方案需要版本 2.1 或更高版本的[工作区加入客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacct-computer-account"></a>如何滚动更新 `AZUREADSSOACCT` 计算机帐户的 Kerberos 解密密钥？

请务必在本地 AD 林中频繁滚动更新 `AZUREADSSOACCT` 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。

>[!IMPORTANT]
>强烈建议至少每 30 天滚动更新一次 Kerberos 解密密钥。

在运行 Azure AD Connect 的本地服务器上执行以下步骤：

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步骤 1。 获取已在其中启用了无缝 SSO 的 AD 林列表

1. 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
4. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
5. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
6. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>步骤 2. 在设置 Kerberos 解密密钥的每个 AD 林上更新此密钥

1. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 AD 林的域管理员凭据。
2. 调用 `Update-AzureADSSOForest -OnPremCredentials $creds`。 此命令会在此特定 AD 林中更新 `AZUREADSSOACCT` 计算机帐户的 Kerberos 解密密钥，并在 Azure AD 中对其进行更新。
3. 针对已设置了此功能的每个 AD 林重复上述步骤。

>[!IMPORTANT]
>确保运行 `Update-AzureADSSOForest` 命令_没有_超过一次。 否则，在用户的 Kerberos 票证过期并由本地 Active Directory 再次发布之前，此功能将停止运行。

## <a name="how-can-i-disable-seamless-sso"></a>如何禁用无缝 SSO？

可使用 Azure AD Connect 禁用无缝 SSO。

运行 Azure AD Connect，选择“更改用户登录页”，并单击“下一步”。 然后取消选中“启用单一登录”选项。 在向导中继续操作。 完成向导后，租户即禁用了无缝 SSO。

但是，屏幕上会显示以下消息：

“单一登录现已禁用，但可执行其他手动步骤以完成清理。 了解详细信息”

若要完成此过程，请在运行 Azure AD Connect 的本地服务器上按以下手动步骤操作：

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步骤 1。 获取已在其中启用了无缝 SSO 的 AD 林列表

1. 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
4. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
5. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
6. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>步骤 2. 从列表中你所看到的每个 AD 林中手动删除 `AZUREADSSOACCT` 计算机帐户。

## <a name="next-steps"></a>后续步骤

- [快速入门](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [**深入技术探究**](active-directory-aadconnect-sso-how-it-works.md) - 了解此功能的工作原理。
- [**故障排除**](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

