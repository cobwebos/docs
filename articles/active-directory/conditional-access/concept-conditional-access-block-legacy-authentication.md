---
title: 在 Azure AD 中阻止旧版身份验证协议
description: 了解组织应如何阻止旧身份验证协议
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dc8381fe964ce924ed37d6b7e6d22dc730eae89
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453051"
---
# <a name="blocking-legacy-authentication"></a>阻止旧身份验证
 
为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 旧身份验证是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的旧版 Office 客户端（例如，Office 2010 客户端）
- 任何使用旧版邮件协议的客户端，例如 IMAP/SMTP/POP3

如今，所有泄密的登录尝试都来自传统身份验证。 旧身份验证不支持多重身份验证（MFA）。 即使在目录中启用了 MFA 策略，损坏的执行组件也可以使用旧版协议进行身份验证并绕过 MFA。 保护你的帐户不受传统协议发出的恶意身份验证请求的最佳方式是完全阻止这些尝试。

## <a name="identify-legacy-authentication-use"></a>确定旧身份验证使用情况

你需要先了解你的用户是否具有使用旧身份验证的应用程序，以及它如何影响你的整个目录，然后才能在目录中阻止旧身份验证。 Azure AD 登录日志可用于了解你是否正在使用旧身份验证。

1. 在 > 登录 Azure Active Directory 导航到 Azure 门户 >。
1. 通过单击 "客户端应用" > 的 "列"，添加 "客户端应用" 列。
1. 按客户端应用 > 其他客户端进行筛选，然后单击 "应用"。

筛选只显示旧版身份验证协议进行的登录尝试。 单击每次登录尝试都将显示其他详细信息。 "基本信息" 选项卡下的 "客户端应用" 字段将指示使用的是旧的身份验证协议。 这些日志将指示哪些用户仍会依赖于旧身份验证，以及哪些应用程序使用旧版协议发出身份验证请求。 对于未出现在这些日志中并且已确认不使用旧身份验证的用户，请执行条件性访问策略或启用基线策略：仅阻止这些用户的旧身份验证。

## <a name="moving-away-from-legacy-authentication"></a>离开旧身份验证 

更好地了解在目录中使用旧身份验证以及哪些应用程序依赖于该身份验证后，下一步是将用户升级为使用新式身份验证。 新式身份验证是标识管理的一种方法，提供更安全的用户身份验证和授权。 如果在目录中部署了 MFA 策略，新式身份验证将确保在需要时提示用户进行 MFA。 这是旧式身份验证协议的更安全替代方法。

本部分提供有关如何将环境更新为新式身份验证的分步概述。 在组织中启用旧版身份验证阻止策略之前，请通读以下步骤。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步骤1：在目录中启用新式身份验证

启用新式身份验证的第一步是确保目录支持新式身份验证。 默认情况下，对于在2017年8月1日或之后创建的目录，会启用新式身份验证。 如果你的目录是在此日期之前创建的，则需要使用以下步骤手动为你的目录启用新式身份验证：

1. 通过运行 " [Skype For Business Online PowerShell" 模块](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)，查看目录是否已支持新式身份验证。
1. 如果命令返回空 @ no__t-0 @ no__t-1property，则会禁用新式身份验证。 使用 @ no__t 更新设置以启用新式身份验证。 如果 @ no__t-0 @ no__t-1property 包含一项，就可以开始了。

请确保在继续操作之前完成此步骤。 首先更改目录配置很重要，因为它们规定了将由所有 Office 客户端使用的协议。 即使你使用的是支持新式身份验证的 Office 客户端，如果在你的目录中禁用了新式身份验证，则它们将默认为使用旧协议。

### <a name="step-2-office-applications"></a>步骤2： Office 应用程序

在目录中启用新式身份验证后，可以通过对 Office 客户端启用新式身份验证开始更新应用程序。 默认情况下，Office 2016 或更高版本的客户端支持新式身份验证。 不需要额外的步骤。

如果你使用的是 Office 2013 Windows 客户端或更低版本，我们建议升级到 Office 2016 或更高版本。 即使完成了在目录中启用新式身份验证的之前步骤，旧的 Office 应用程序仍将继续使用旧的身份验证协议。 如果你使用的是 Office 2013 客户端，但无法立即升级到 Office 2016 或更高版本，请按照以下文章中的步骤在 [Windows 设备上启用 Office 2013 新式身份验证](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 若要在使用旧身份验证时帮助保护你的帐户，我们建议你在目录中使用强密码。 查看 [Azure AD 密码保护](../authentication/concept-password-ban-bad.md) to 禁止在目录中执行弱密码。

Office 2010 不支持新式身份验证。 需要使用 Office 2010 将任何用户升级到最新版本的 Office。 建议升级到 Office 2016 或更高版本，因为默认情况下它会阻止旧身份验证。

如果你使用的是 MacOS，则建议升级到适用于 Mac 2016 或更高版本的 Office。 如果你使用的是本机邮件客户端，则需要在所有设备上安装 MacOS 版本10.14 或更高版本。

### <a name="step-3-exchange-and-sharepoint"></a>步骤3： Exchange 和 SharePoint

对于基于 Windows 的 Outlook 客户端，若要使用新式身份验证，还必须启用 "Exchange Online"。 如果为 Exchange Online 禁用了新式身份验证，则支持新式身份验证的基于 Windows 的 Outlook 客户端（Outlook 2013 或更高版本）将使用基本身份验证连接到 Exchange Online 邮箱。

对于新式身份验证默认值，启用 SharePoint Online。 对于2017年8月1日之后创建的目录，默认情况下，Exchange Online 中会启用新式身份验证。 但是，如果你先前禁用了新式身份验证，或者你使用的是在此日期之前创建的目录，请按照以下文章中的步骤 [在 Exchange Online 中启用新式身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步骤4： Skype for Business

若要防止 Skype for business 发出旧身份验证请求，需要为 Skype for business Online 启用新式验证。 对于在2017年8月1日之后创建的目录，默认情况下会启用对 Skype for Business 的新式身份验证。

建议你过渡到支持新式身份验证的 Microsoft 团队。 但是，如果此时无法迁移，则需要为 Skype for business Online 启用新式身份验证，以便 Skype for business 客户端开始使用新式身份验证。 请按照本文中的步骤 [使用新式身份验证支持的商业拓扑](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，为 skype for Business 启用新式身份验证。

除了为 Skype for business Online 启用新式身份验证外，我们还建议在为 Skype for business 启用新式验证时，为 Exchange Online 启用新式验证。 此过程将帮助同步 Exchange Online 和 Skype for business online 中新式身份验证的状态，并将阻止 Skype for business 客户端的多个登录提示。

### <a name="step-5-using-mobile-devices"></a>步骤5：使用移动设备

移动设备上的应用程序也需要阻止旧式身份验证。 建议使用 Outlook for Mobile。 默认情况下，Outlook for Mobile 支持新式身份验证，并将满足其他 MFA 基线保护策略。

若要使用本机 iOS 邮件客户端，你将需要运行 iOS 版本11.0 或更高版本，以确保邮件客户端已更新为阻止旧身份验证。

### <a name="step-6-on-premises-clients"></a>步骤6：本地客户端

如果你是使用本地 Exchange Server 的混合客户和本地 Skype for business，则这两项服务都需要更新以启用新式身份验证。 在混合环境中使用新式身份验证时，仍会对本地用户进行身份验证。 授权其访问资源（文件或电子邮件）更改的故事。

在本地开始启用新式身份验证之前，请确保已满足先决条件。 你现在已准备好在本地启用新式身份验证。

有关启用新式身份验证的步骤，请参阅以下文章：

* [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何将新式身份验证（ADAL）用于 Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>后续步骤

- [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何将新式身份验证（ADAL）用于 Skype for Business](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [阻止旧身份验证](block-legacy-authentication.md)
