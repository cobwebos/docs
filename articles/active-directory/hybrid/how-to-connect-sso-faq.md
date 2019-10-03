---
title: Azure AD Connect：无缝单一登录 - 常见问题 | Microsoft Docs
description: 提供了有关 Azure Active Directory 无缝单一登录常见问题的解答。
services: active-directory
keywords: 什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176662"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 无缝单一登录：常见问题

本文解决了有关 Azure Active Directory 无缝单一登录（无缝 SSO）的常见问题。 请随时返回查看新内容。

**问：无缝 SSO 使用的登录方法**

无缝 SSO 可与[密码哈希同步](how-to-connect-password-hash-synchronization.md)或[传递身份验证](how-to-connect-pta.md)登录方法结合使用。 但是，此功能不能用于 Active Directory 联合身份验证服务 (ADFS)。

**问：无缝 SSO 是否为免费功能？**

Seamless SSO 是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。

**问：[Microsoft Azure 德国云和](https://www.microsoft.de/cloud-deutschland) [Microsoft Azure 政府云中](https://azure.microsoft.com/features/gov/)是否提供无缝 SSO？**

否。 无缝 SSO 只能在全球范围内的 Azure AD 实例中使用。

**问：哪些应用程序利用无`domain_hint`缝`login_hint` SSO 的或参数功能？**

以下非详尽列表列出了可将这些参数发送到 Azure AD 的部分应用程序，因此使用无缝 SSO 可为用户提供无提示登录体验（即用户无需输入其用户名或密码）：

| 应用程序名 | 可供使用的应用程序 URL |
| -- | -- |
| 访问面板 | https:\//myapps.microsoft.com/contoso.com |
| Outlook 网页版 | https：\//outlook.office365.com/contoso.com |
| Office 365 门户 | https：\//portal.office.com？ domain_hint = contoso，https：\//www.office.com？ domain_hint = contoso .com |

此外, 如果应用程序将登录请求发送到 Azure AD 的终结点 (即, https:\//login.microsoftonline.com/contoso.com/<), 则用户将获得无提示登录体验。> 或 https:\//login.microsoftonline.com/<tenant_ID>/<..>-而不是 Azure AD 公用终结点, 即 https:\//login.microsoftonline.com/common/<...>。 以下为提出此类登录请求的应用程序的非详尽列表。

| 应用程序名 | 可供使用的应用程序 URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure 门户 | https:\//portal.azure.com/contoso.com |

在上述表中，将“contoso.com”替换为域名，为租户获取正确的应用程序 URL。

如果希望其他应用程序具备无提示登录体验，请在反馈部分告知我们。

**问：无缝 SSO 是否`Alternate ID`支持作为用户名， `userPrincipalName`而不是？**

是。 在 Azure AD Connect 中进行配置时，无缝 SSO 支持将 `Alternate ID` 作为用户名，如[此处](how-to-connect-install-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅特定应用程序文档。

**问：[Azure AD 联接](../active-directory-azureadjoin-overview.md)与无缝 SSO 提供的单一登录体验有何区别？**

如果用户的设备注册了 Azure AD，[Azure AD 加入](../active-directory-azureadjoin-overview.md)会向用户提供 SSO。 这些设备不必已加入域。 使用主刷新令牌或 PRT（而非 Kerberos）提供 SSO。 用户体验在 Windows 10 设备上最佳。 在 Microsoft Edge 浏览器上，SSO 会自动实现。 也可在 Chrome 上实现，但需要使用浏览器扩展插件。

可同时在租户上使用 Azure AD 加入和无缝 SSO。 这两种功能互补。 如果同时开启这两项功能，将先通过 Azure AD 加入实现 SSO，再进行无缝 SSO。

**问：我想要在 Azure AD 中注册非 Windows 10 设备，但不使用 AD FS。是否可以改用无缝 SSO？**

可以，此方案需要版本 2.1 或更高版本的[工作区加入客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

**问：如何滚动更新`AZUREADSSOACC`计算机帐户的 Kerberos 解密密钥？**

请务必在本地 AD 林中频繁滚动更新 `AZUREADSSOACC` 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。

>[!IMPORTANT]
>强烈建议至少每 30 天滚动更新一次 Kerberos 解密密钥。

在运行 Azure AD Connect 的本地服务器上执行以下步骤：

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
    2. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
    3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
    4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
    5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

    **步骤2。在已设置的每个 AD 林上更新 Kerberos 解密密钥**

    1. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 AD 林的域管理员凭据。

    > [!NOTE]
    > 我们使用以用户主体名称 (UPN) (johndoe@contoso.com) 格式或域限定的 SAM 帐户名（contoso\johndoe 或 contoso.com\johndoe）格式提供的域管理员用户名查找目标 AD 林。 如果你使用域限定的 SAM 帐户名，则我们使用用户名的域部分[使用 DNS 查找域管理员的域控制器](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)。 如果你使用的是 UPN，则我们在查找合适的域控制器前会[将它转换为域限定的 SAM 帐户名](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)。

    2. 调用 `Update-AzureADSSOForest -OnPremCredentials $creds`。 此命令会在此特定 AD 林中更新 `AZUREADSSOACC` 计算机帐户的 Kerberos 解密密钥，并在 Azure AD 中对其进行更新。
    3. 针对已设置了此功能的每个 AD 林重复上述步骤。

    >[!IMPORTANT]
    >确保运行 `Update-AzureADSSOForest` 命令_没有_超过一次。 否则，在用户的 Kerberos 票证过期并由本地 Active Directory 再次发布之前，此功能将停止运行。

**问：如何禁用无缝 SSO？**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
    2. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
    3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
    4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
    5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

    **步骤3。从列出的`AZUREADSSOACCT`每个 AD 林中手动删除计算机帐户。**

## <a name="next-steps"></a>后续步骤

- [**快速入门**](how-to-connect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [深入技术探究](how-to-connect-sso-how-it-works.md) - 了解此功能如何运作。
- [**故障排除**](tshoot-connect-sso.md) - 了解如何解决此功能的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
