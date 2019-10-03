---
title: 基线策略：阻止旧式身份验证（预览版）- Azure Active Directory
description: 用于阻止旧式身份验证协议的条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b74491c76747bd635547f6f22964fe792dbf01a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104087"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>基线策略：阻止旧式身份验证（预览版）

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 术语“旧式身份验证”是指通过以下方式发出的身份验证请求：

* 不使用新式身份验证的早期 Office 客户端（例如 Office 2010 客户端）
* 使用 IMAP/SMTP/POP3 等旧式邮件协议的任何客户端

当今，大部分透露信息的登录企图都来自旧式身份验证。 旧式身份验证不支持多重身份验证 (MFA)。 即使在目录中启用 MFA 策略，恶意行动者也可以使用旧式协议进行身份验证并绕过 MFA。

防范旧式协议发出的恶意身份验证请求入侵帐户的最佳方法是完全阻止这些企图。 为了方便你保护环境，我们创建了此基线策略来阻止旧式身份验证。

“阻止旧式身份验证”是一个[基线策略](concept-baseline-protection.md)，它可以阻止旧式协议发出的所有身份验证请求。 所有用户必须使用新式身份验证才能成功登录。 将此策略与其他基线策略结合使用时，可以阻止来自旧式协议的所有请求，在必要的情况下，所有用户都需要执行 MFA。 此策略不会阻止 Exchange ActiveSync。

## <a name="identify-legacy-authentication-use"></a>识别旧式身份验证的用法

需要先了解用户是否有使用旧式身份验证的应用，以及它如何影响整个目录，然后才能在目录中阻止旧式身份验证。 可以使用 Azure AD 登录日志来了解是否正在使用旧式身份验证。

1. 导航到“Azure 门户” > “Azure Active Directory” > “登录”。
1. 单击“列” > “客户端应用”添加“客户端应用”列（如果未显示）。
1. 单击“添加筛选器” > “客户端应用”> 选择“其他客户端”的所有选项，然后单击“应用”。

筛选将仅显示旧式身份验证协议进行的登录尝试。 单击每个单独的登录尝试将显示其他详细信息。 “基本信息”选项卡下的“客户端应用”字段将指示使用了哪个旧式身份验证协议。

这些日志将指示哪些用户仍然依赖于旧式身份验证，以及哪些应用程序使用旧式协议发出身份验证请求。 对于未出现在这些日志中且已确认不使用旧身份验证的用户，请仅为这些用户实施条件访问策略，或启用“基线策略: 阻止旧式身份验证”。

## <a name="moving-away-from-legacy-authentication"></a>弃用旧式身份验证

在更好地了解谁正在你的目录中使用旧式身份验证以及哪些应用程序依赖于这种身份验证后，下一步是将用户升级为使用新式身份验证。 新式身份验证是一种标识管理方法，提供更安全的用户身份验证和授权。 如果在目录中实施了 MFA 策略，则新式身份验证可确保根据需要提示用户执行 MFA。 这是旧式身份验证协议的更安全替代协议。

本部分逐步概述如何将环境更新到新式身份验证。 在组织中启用旧式身份验证阻止策略之前，请仔细阅读以下步骤。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步骤 1：在目录中启用新式身份验证

启用新式身份验证的第一步是确保目录支持新式身份验证。 对于在 2017 年 8 月 1 日或之后创建的目录，默认已启用新式身份验证。 如果目录是在此日期之前创建的，则需要使用以下步骤手动为目录启用新式身份验证：

1. 通过在 [Skype for Business Online PowerShell 模块](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)中运行 `Get-CsOAuthConfiguration` 来检查目录是否支持新式身份验证。
1. 如果该命令返回空的 `OAuthServers` 属性，则表示新式身份验证已禁用。 使用 `Set-CsOAuthConfiguration` 更新设置以启用新式身份验证。 如果 `OAuthServers` 属性包含某个项，则可以继续下一步。

请务必先完成此步骤，然后继续。 首先更改目录配置，这一点至关重要，因为这些配置规定了所有 Office 客户端要使用哪个协议。 即使使用的 Office 客户端支持新式身份验证，但如果在目录中禁用了新式身份验证，这些客户端也会默认使用旧式协议。

### <a name="step-2-office-applications"></a>步骤 2：Office 应用程序

在目录中启用新式身份验证后，可以通过对 Office 客户端启用新式身份验证开始更新应用程序。 默认情况下，Office 2016 或更高版本的客户端支持新式身份验证。 无需执行额外的步骤。

如果使用 Office 2013 Windows 客户端或更低版本，我们建议升级到 Office 2016 或更高版本。 即使完成了上述在目录中启用新式身份验证的步骤，早期的 Office 应用程序也仍会继续使用旧式身份验证协议。 如果使用 Office 2013 客户端且无法立即升级到 Office 2016 或更高版本，请遵循以下文章中的步骤[在 Windows 设备上为 Office 2013 启用新式身份验证](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 为了帮助你在使用旧式身份验证时保护帐户，我们建议对整个目录使用强密码。 请查看[Azure AD 密码保护](../authentication/concept-password-ban-bad.md), 禁止在目录中执行弱密码。

Office 2010 不支持新式身份验证。 需将使用 Office 2010 的任何用户升级到最新版本的 Office。 我们建议升级到 Office 2016 或更高版本，因为它默认会阻止旧式身份验证。

如果使用 MacOS，我们建议升级到 Office for Mac 2016 或更高版本。 如果使用本机邮件客户端，则需要在所有设备上安装 MacOS 10.14 或更高版本。

### <a name="step-3-exchange-and-sharepoint"></a>步骤 3：Exchange 和 SharePoint

要让基于 Windows 的 Outlook 客户端使用新式身份验证，还必须为 Exchange Online 启用新式身份验证。 如果对 Exchange Online 禁用新式身份验证，则支持新式身份验证的基于 Windows 的 Outlook 客户端（Outlook 2013 或更高版本）将使用基本身份验证连接到 Exchange Online 邮箱。

默认已经为 SharePoint Online 启用了新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已在 Exchange Online 中启用新式身份验证。 但是，如果你先前禁用了新式身份验证，或者使用的目录是在此日期之前创建的，请遵循以下文章中的步骤[在 Exchange Online 中启用新式身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步骤 4：Skype for Business

若要阻止 Skype for business 发出的旧式身份验证请求，需要为 Skype for Business Online 启用新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已经为 Skype for Business 启用了新式身份验证。

建议过渡到 Microsoft Teams，它默认支持新式身份验证。 但是，如果当前无法迁移，则需要为 Skype for Business Online 启用新式身份验证，让 Skype for Business 客户端开始使用新式身份验证。 遵循[新式身份验证支持的 Skype for Business 拓扑](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)一文中的步骤，为 Skype for Business 启用新式身份验证。

除了为 Skype for Business Online 启用新式身份验证以外，我们还建议在为 Skype for Business 启用新式身份验证时，为 Exchange Online 启用新式身份验证。 此过程有助于同步 Exchange Online 和 Skype for Business Online 中的新式身份验证状态，并防止 Skype for Business 客户端中多次出现登录提示。

### <a name="step-5-using-mobile-devices"></a>步骤 5：使用移动设备

移动设备上的应用程序也需要阻止旧式身份验证。 我们建议使用 Outlook for Mobile。 Outlook for Mobile 默认支持新式身份验证，可满足其他 MFA 基线保护策略。

若要使用本机 iOS 邮件客户端，需要运行 iOS 11.0 或更高版本，以确保更新邮件客户端来阻止旧式身份验证。

### <a name="step-6-on-premises-clients"></a>步骤 6：本地客户端

对于使用本地 Exchange Server 和本地 Skype for Business 的混合产品客户，需要更新这两个服务才能启用新式身份验证。 在混合环境中使用新式身份验证时，仍会在本地对用户进行身份验证。 授权用户访问资源（文件或电子邮件）的历程发生了变化。

在本地开始启用新式身份验证之前，请确保符合先决条件。
现在，可以在本地启用新式身份验证。

可在以下文章中找到有关启用新式身份验证的步骤：

* [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何在 Skype for Business 中使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略“基线策略:阻止旧式身份验证(预览版)”已预配置，当你在 Azure 门户中导航到“条件访问”边栏选项卡时，它会显示在顶部。 此设置只会在成功登录后生效，因此，用户仍可以选择尝试使用旧式身份验证。

若要启用此策略并保护组织：

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 在策略列表中选择“基线策略:阻止旧式身份验证(预览版)”。
1. 将“启用策略”设置为“立即使用策略”。
1. 单击“保存”。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
