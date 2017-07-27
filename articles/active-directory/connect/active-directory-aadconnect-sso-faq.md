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
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: e363ade43ab9e2b2432c9efdc3ce1b661e278b2a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 无缝单一登录：常见问题

本文解决了有关 Azure AD 无缝 SSO 的常见问题。 请随时返回查看更新内容。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>无缝 SSO 使用的登录方法是什么？

无缝 SSO 可与[密码哈希同步](active-directory-aadconnectsync-implement-password-synchronization.md)或[直通身份验证](active-directory-aadconnect-pass-through-authentication.md)登录方法结合使用，但不能与 Active Directory 联合身份验证服务 (AD FS) 一同使用。

## <a name="is-seamless-sso-a-free-feature"></a>无缝 SSO 是否为免费功能？

Seamless SSO 是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。 功能正式发布时仍然可以免费使用。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>哪些应用程序可以利用无缝 SSO 的 `domain_hint` 或 `login_hint` 参数功能？

我们正在编译能够/无法发送这些参数的应用程序列表。 如果你有感兴趣的应用程序，请在备注部分告诉我们。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>无缝 SSO 是否支持将 `Alternate ID` 作为用户名（而不是 `userPrincipalName`）？

是的。 在 Azure AD Connect 中进行配置时，无缝 SSO 支持将 `Alternate ID` 作为用户名，如[此处](active-directory-aadconnect-get-started-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅具体应用程序文档。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>我想要在 Azure AD 中注册非 Windows 10 设备，但不使用 AD FS。 是否可以改为使用无缝 SSO？

可以，此方案需要版本 2.1 或更高版本的[工作区加入客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

## <a name="how-can-i-disable-seamless-sso"></a>如何禁用无缝 SSO？

可使用 Azure AD Connect 禁用无缝 SSO。

运行 Azure AD Connect，选择“更改用户登录页”，然后单击“下一步”。 然后取消选中“启用单一登录”选项。 在向导中继续操作。 完成向导后，租户即禁用了无缝 SSO。

但是，屏幕上会显示以下消息：

“单一登录现已禁用，但可执行其他手动步骤以完成清理。 了解详细信息”

以下是需要执行的手动步骤：

1. 获取已在其中启用了无缝 SSO 的 AD 林列表
- 首先，下载并安装 [Microsoft Online Services 登录助手](http://go.microsoft.com/fwlink/?LinkID=286152)。
- 然后下载并安装[用于 Windows PowerShell 的 64 位 Azure Active Directory 模块](http://go.microsoft.com/fwlink/p/?linkid=236297)。
- 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
- 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
  - 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令应可提供一个弹出窗口，用于输入 Azure AD 租户管理员凭据。
  - 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。
2. 从列表中你所看到的每个 AD 林中手动删除 `AZUREADSSOACCT` 计算机帐户。

## <a name="next-steps"></a>后续步骤

- [**快速入门**](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [**深入技术探究**](active-directory-aadconnect-sso-how-it-works.md) - 了解此功能的工作原理。
- [**故障排除**](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

