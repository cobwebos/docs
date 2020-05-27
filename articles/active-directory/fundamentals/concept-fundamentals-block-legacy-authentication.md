---
title: 在 Azure AD 中阻止旧式身份验证协议
description: 了解组织如何以及为何要阻止旧式身份验证协议
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1799f676e8971726832cc50598e119f029bc331d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196390"
---
# <a name="blocking-legacy-authentication"></a>阻止旧式身份验证
 
为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧式身份验证。 术语“旧式身份验证”是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的早期 Office 客户端（例如 Office 2010 客户端）
- 使用 IMAP/SMTP/POP3 等旧式邮件协议的任何客户端

当今，大部分存在危害性的登录企图都来自旧式身份验证。 旧式身份验证不支持多重身份验证 (MFA)。 即使在目录中启用 MFA 策略，恶意行动者也可以使用旧式协议进行身份验证并绕过 MFA。 防范旧式协议发出的恶意身份验证请求入侵帐户的最佳方法是完全阻止这些企图。

## <a name="identify-legacy-authentication-use"></a>识别是否以及如何使用了旧式身份验证

在目录中阻止旧式身份验证之前，需要先了解用户是否有使用旧式身份验证的应用，以及它如何影响整个目录。 可以使用 Azure AD 登录日志来了解是否正在使用旧式身份验证。

1. 导航到“Azure 门户”>“Azure Active Directory”>“登录”。 ****   ****   ****
1. 如果未显示“客户端应用”列，请单击“列”>“客户端应用”添加该列。 ****   ****
1. 按“客户端应用”进行筛选，然后选中显示的所有“旧式身份验证客户端”选项。 ****  
1. 按“状态” > “成功”进行筛选。  
1. 如果需要，请使用“日期”筛选器扩展日期范围。

筛选将仅显示通过所选旧式身份验证协议成功进行的登录尝试。 单击每个登录尝试会显示更多详细信息。 选择单个数据行后，“基本信息”选项卡下的“客户端应用”列或“客户端应用”字段将指示使用了哪个旧式身份验证协议。 这些日志将指示哪些用户仍然依赖于旧式身份验证，以及哪些应用程序正在使用旧式协议发出身份验证请求。 对于未出现在这些日志中且已被确认不使用旧式身份验证的用户，请仅为这些用户实施条件访问策略，或启用“基线策略: 阻止旧式身份验证”。

## <a name="moving-away-from-legacy-authentication"></a>弃用旧式身份验证 

在更好地了解谁正在你的目录中使用旧式身份验证以及哪些应用程序依赖于这种身份验证后，下一步是将用户升级为使用新式身份验证。 新式身份验证是一种标识管理方法，它提供更安全的用户身份验证和授权。 如果在目录中实施了 MFA 策略，则新式身份验证可确保在需要时提示用户执行 MFA。 这是旧式身份验证协议的更安全的替代方法。

本部分逐步概述如何将环境更新到新式身份验证。 在组织中启用旧式身份验证阻止策略之前，请仔细阅读以下步骤。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步骤 1：在目录中启用新式身份验证

启用新式身份验证的第一步是确保目录支持新式身份验证。 对于在 2017 年 8 月 1 日或之后创建的目录，默认已启用新式身份验证。 如果目录是在此日期之前创建的，则需要使用以下步骤手动为目录启用新式身份验证：

1. 通过在 [Skype for Business Online PowerShell 模块](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)中运行 `Get-CsOAuthConfiguration` 来检查目录是否支持新式身份验证。
1. 如果该命令返回空的 `OAuthServers` 属性，则表示新式身份验证已禁用。 使用 `Set-CsOAuthConfiguration` 更新设置以启用新式身份验证。 如果 `OAuthServers` 属性包含某个项，则可以继续下一步。

请务必先完成此步骤，然后继续。 首先更改目录配置，这一点至关重要，因为这些配置规定了所有 Office 客户端要使用哪个协议。 即使使用的 Office 客户端支持新式身份验证，但如果在目录中禁用了新式身份验证，这些客户端也会默认使用旧式协议。

### <a name="step-2-office-applications"></a>步骤 2：Office 应用程序

在目录中启用新式身份验证后，可以通过对 Office 客户端启用新式身份验证开始更新应用程序。 默认情况下，Office 2016 或更高版本的客户端支持新式身份验证。 无需执行额外的步骤。

如果使用 Office 2013 Windows 客户端或更低版本，我们建议升级到 Office 2016 或更高版本。 即使完成了上述在目录中启用新式身份验证的步骤，早期的 Office 应用程序也仍会继续使用旧式身份验证协议。 如果使用 Office 2013 客户端且无法立即升级到 Office 2016 或更高版本，请按照 [在 Windows 设备上为 Office 2013 启用新式身份验证](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)一文中的步骤操作。 为了帮助你在使用旧式身份验证时保护帐户，我们建议对整个目录使用强密码。 查看 [Azure AD 密码保护](../authentication/concept-password-ban-bad.md)了解如何在整个目录中禁止弱密码。

Office 2010 不支持新式身份验证。 需要将使用 Office 2010 的任何用户升级到更高版本的 Office。 我们建议升级到 Office 2016 或更高版本，因为它默认会阻止旧式身份验证。

如果使用 macOS，我们建议升级到 Office for Mac 2016 或更高版本。 如果使用本机邮件客户端，则需要在所有设备上安装 macOS 10.14 或更高版本。

### <a name="step-3-exchange-and-sharepoint"></a>步骤 3：Exchange 和 SharePoint

要让基于 Windows 的 Outlook 客户端使用新式身份验证，还必须为 Exchange Online 启用新式身份验证。 如果对 Exchange Online 禁用新式身份验证，则支持新式身份验证的基于 Windows 的 Outlook 客户端（Outlook 2013 或更高版本）将使用基本身份验证连接到 Exchange Online 邮箱。

SharePoint Online 在默认情况下已启用新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已在 Exchange Online 中启用新式身份验证。 但是，如果你先前禁用了新式身份验证，或者使用的目录是在此日期之前创建的，请按照 [在 Exchange Online 中启用新式身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)一文中的步骤操作。

### <a name="step-4-skype-for-business"></a>步骤 4：Skype for Business

若要阻止 Skype for Business 发出的旧式身份验证请求，需要为 Skype for Business Online 启用新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已经为 Skype for Business 启用了新式身份验证。

建议过渡到 Microsoft Teams，它默认支持新式身份验证。 但是，如果当前无法迁移，则需要为 Skype for Business Online 启用新式身份验证，让 Skype for Business 客户端开始使用新式身份验证。 遵循 [新式身份验证支持的 Skype for Business 拓扑](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)一文中的步骤，为 Skype for Business 启用新式身份验证。

除了为 Skype for Business Online 启用新式身份验证以外，我们还建议在为 Skype for Business 启用新式身份验证时，为 Exchange Online 启用新式身份验证。 此过程有助于同步 Exchange Online 和 Skype for Business Online 中的新式身份验证状态，并防止 Skype for Business 客户端中多次出现登录提示。

### <a name="step-5-using-mobile-devices"></a>步骤 5：使用移动设备

移动设备上的应用程序也需要阻止旧式身份验证。 我们建议使用 Outlook for Mobile。 Outlook for Mobile 默认支持新式身份验证，可满足其他 MFA 基线保护策略。

若要使用本机 iOS 邮件客户端，需要运行 iOS 11.0 或更高版本，以确保更新邮件客户端来阻止旧式身份验证。

### <a name="step-6-on-premises-clients"></a>步骤 6：本地客户端

对于使用本地 Exchange Server 和本地 Skype for Business 的混合产品客户，需要更新这两个服务才能启用新式身份验证。 在混合环境中使用新式身份验证时，仍会在本地对用户进行身份验证。 授权用户访问资源（文件或电子邮件）的过程发生了变化。

在本地开始启用新式身份验证之前，请确保符合先决条件。 现在可以在本地启用新式身份验证了。

可在以下文章中找到有关启用新式身份验证的步骤：

* [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何在 Skype for Business 中使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>后续步骤

- [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何在 Skype for Business 中使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [阻止旧式身份验证](../conditional-access/block-legacy-authentication.md)
