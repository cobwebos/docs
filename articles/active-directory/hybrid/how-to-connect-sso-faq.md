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
ms.topic: how-to
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea5c3e0ffc000d3d239e87e9771d1b49d98fd206
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589038"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 无缝单一登录：常见问题

本文解决了有关 Azure Active Directory 无缝单一登录（无缝 SSO）的常见问题。 请随时返回查看新内容。

**问：无缝 SSO 使用的登录方法是什么**

无缝 SSO 可与[密码哈希同步](how-to-connect-password-hash-synchronization.md)或[传递身份验证](how-to-connect-pta.md)登录方法结合使用。 但是，此功能不能用于 Active Directory 联合身份验证服务 (ADFS)。

**问：无缝 SSO 是否为免费功能？**

Seamless SSO 是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用此功能。

**问：能否在 [Microsoft Azure 德国云](https://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中使用无缝 SSO？**

无缝 SSO 可用于 Azure 政府云。 有关详细信息，请查看[针对 Azure 政府的混合标识注意事项](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud)。

**问：哪些应用程序可以利用无缝 SSO 的 `domain_hint` 或 `login_hint` 参数功能？**

以下非详尽列表列出了可将这些参数发送到 Azure AD 的部分应用程序，因此使用无缝 SSO 可为用户提供无提示登录体验（即用户无需输入其用户名或密码）：

| 应用程序名称 | 可供使用的应用程序 URL |
| -- | -- |
| 访问面板 | https:\//myapps.microsoft.com/contoso.com |
| Outlook 网页版 | https:\//outlook.office365.com/contoso.com |
| Office 365 门户 | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

此外，如果应用程序将登录请求发送到设置为租户的 Azure AD 的终结点（即，https:\//login.microsoftonline.com/contoso.com/<..> 或 https:\//login.microsoftonline.com/<tenant_ID>/<..>）而非 Azure AD 的常用终结点（即，https:\//login.microsoftonline.com/common/<...>），则用户将获得无提示登录体验。 以下为提出此类登录请求的应用程序的非详尽列表。

| 应用程序名称 | 可供使用的应用程序 URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure 门户 | https:\//portal.azure.com/contoso.com |

在上述表中，将“contoso.com”替换为域名，为租户获取正确的应用程序 URL。

如果希望其他应用程序具备无提示登录体验，请在反馈部分告知我们。

**问：无缝 SSO 是否支持将 `Alternate ID` 作为用户名（而不是 `userPrincipalName`）？**

是的。 在 Azure AD Connect 中进行配置时，无缝 SSO 支持将 `Alternate ID` 作为用户名，如[此处](how-to-connect-install-custom.md)所示。 并非所有 Office 365 应用程序都支持 `Alternate ID`。 有关支持声明，请参阅特定应用程序文档。

**问：[Azure AD 加入](../active-directory-azureadjoin-overview.md)与无缝 SSO 提供的单一登录体验有何不同？**

如果用户的设备注册了 Azure AD，[Azure AD 加入](../active-directory-azureadjoin-overview.md)会向用户提供 SSO。 这些设备不必已加入域。 使用主刷新令牌或 PRT（而非 Kerberos）提供 SSO。  用户体验在 Windows 10 设备上最佳。 在 Microsoft Edge 浏览器上，SSO 会自动实现。 也可在 Chrome 上实现，但需要使用浏览器扩展插件。

可同时在租户上使用 Azure AD 加入和无缝 SSO。 这两种功能互补。 如果同时开启这两项功能，将先通过 Azure AD 加入实现 SSO，再进行无缝 SSO。

**问：我想要在 Azure AD 中注册非 Windows 10 设备，但不使用 AD FS。是否可以改为使用无缝 SSO？**

可以，此方案需要版本 2.1 或更高版本的[工作区加入客户端](https://www.microsoft.com/download/details.aspx?id=53554)。

**问：如何滚动更新 `AZUREADSSO` 计算机帐户的 Kerberos 解密密钥？**

请务必在本地 AD 林中频繁滚动更新 `AZUREADSSO` 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。

>[!IMPORTANT]
>强烈建议至少每 30 天滚动更新一次 Kerberos 解密密钥。

在运行 Azure AD Connect 的本地服务器上执行以下步骤：

   **步骤 1。获取已在其中启用了无缝 SSO 的 AD 林列表**

   1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
   2. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
   3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
   4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
   5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

   **步骤 2.在设置 Kerberos 解密密钥的每个 AD 林上更新此密钥**

   1. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 AD 林的域管理员凭据。

   > [!NOTE]
   >必须以 SAM 帐户名称格式（contoso\johndoe 或 contoso.com\johndoe）输入域管理员凭据用户名。 我们使用用户名的域部分查找使用 DNS 的域管理员的域控制器。

   >[!NOTE]
   >使用的域管理员帐户不得是受保护用户组的成员。 如果是，则操作将失败。

   2. 调用 `Update-AzureADSSOForest -OnPremCredentials $creds`。 此命令会在此特定 AD 林中更新 `AZUREADSSO` 计算机帐户的 Kerberos 解密密钥，并在 Azure AD 中对其进行更新。
   
   >[!NOTE]
   >如果你不是域管理员，并且域管理员已为你分配了权限，则应该调用 `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`
   
   3. 针对已设置了此功能的每个 AD 林重复上述步骤。

   >[!IMPORTANT]
   >确保运行 `Update-AzureADSSOForest` 命令_没有_超过一次。 否则，在用户的 Kerberos 票证过期并由本地 Active Directory 再次发布之前，此功能将停止运行。

**问：如何禁用无缝 SSO？**

   **步骤 1。在租户上禁用此功能**

   **选项 A：使用 Azure AD Connect 进行禁用**
    
   1. 运行 Azure AD Connect，选择“更改用户登录页”，并单击“下一步”。 
   2. 取消选中“启用单一登录”选项。 在向导中继续操作。

   完成向导后，将在租户上禁用无缝 SSO。 但是，屏幕上将显示以下消息：

   “单一登录现已禁用，但可执行其他手动步骤以完成清理。 了解详细信息”

   若要完成清理过程，请在运行 Azure AD Connect 的本地服务器上执行步骤 2 和 3。

   **选项 B：使用 PowerShell 进行禁用**

   在运行 Azure AD Connect 的本地服务器上运行以下步骤：

   1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
   2. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
   3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
   4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
   5. 调用 `Enable-AzureADSSO -Enable $false`。
   
   此时，无缝 SSO 处于禁用状态，但是，如果你想要启用无缝 SSO，则域仍将保持配置状态。 如果要完全从无缝 SSO 配置中删除域，请在完成上述步骤5后调用以下 cmdlet： `Disable-AzureADSSOForest -DomainFqdn <fqdn>` 。

   >[!IMPORTANT]
   >使用 PowerShell 禁用无缝 SSO 不会更改 Azure AD Connect 中的状态。 无缝 SSO 在“更改用户登录”页面中将显示为已启用。

   **步骤 2.获取已在其中启用了无缝 SSO 的 AD 林列表**

   如果已使用 Azure AD Connect 禁用了无缝 SSO，请依次执行下面的任务 1 到 4。 但是，如果是使用 PowerShell 禁用了无缝 SSO，请跳转到下面的任务 5。

   1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
   2. 导航到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
   3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。
   4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令可提供一个弹出窗口，用以输入租户的全局管理员凭据。
   5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令可提供已在其中启用了此功能的 AD 林列表（请查看“域”列表）。

   **步骤 3.从列表中显示的每个 AD 林中手动删除 `AZUREADSSO` 计算机帐户。**

## <a name="next-steps"></a>后续步骤

- [快速入门](how-to-connect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [深入技术探究](how-to-connect-sso-how-it-works.md) - 了解此功能如何运作。
- [故障排除](tshoot-connect-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。
