---
title: 基线策略阻止旧式身份验证 （预览版）-Azure Active Directory
description: 条件性访问策略来阻止旧式身份验证协议
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
ms.openlocfilehash: e7eebc68ae8a55d636f3bc85e179bd7d6813be8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235559"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>基准策略：块旧式身份验证 （预览版）

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 传统的身份验证是一个术语，指的是身份验证请求所做的：

* 旧式 Office 客户端不使用新式身份验证 （例如，Office 2010 客户端）
* 使用旧邮件协议，如 IMAP/smtp/pop3 任何客户端

现在，所有会损害登录尝试的大部分来自于传统的身份验证。 传统的身份验证不支持多重身份验证 (MFA)。 即使你的目录上启用了 MFA 策略，恶意参与者可以使用旧版协议进行身份验证，并绕过 MFA。

保护你的帐户免受恶意身份验证请求所做的旧协议的最佳方法是阻止这些尝试一起。 若要使你更轻松地阻止通过旧协议所做的所有登录请求，我们创建了正好基线策略。

![使用条件性访问的块旧式身份验证](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**块旧式身份验证**是[基准策略](concept-baseline-protection.md)阻止从旧协议进行的所有身份验证请求。 若要成功登录的所有用户，必须使用新式身份验证。 与其他基线策略结合使用，来自旧协议的所有请求将被都阻止，并将 mfa 需要时，会要求所有用户。 此策略不会阻止 Exchange ActiveSync。

## <a name="identify-legacy-authentication-use"></a>识别旧式身份验证，请使用

在目录中，你可以阻止旧式身份验证之前，需要首先了解用户是否拥有使用传统的身份验证以及它如何影响你的整个目录的应用。 可以使用 azure AD 单一登录日志以了解是否使用旧式身份验证。

1. 导航到**Azure 门户** > **Azure Active Directory** > **登录**。
1. 如果它不显示通过单击添加客户端应用程序列**列** > **客户端应用**。
1. 通过筛选**客户端应用程序** > **其他客户端**然后单击**应用**。

筛选仅显示在登录尝试所做的旧式身份验证协议。 单击每个单独的登录尝试会显示更多详细信息。 **客户端应用程序**字段下**的基本信息**选项卡将指示的旧式身份验证协议。

这些日志将指出哪些用户仍依赖于传统的身份验证以及哪些应用程序使用传统协议进行身份验证请求。 对于不会显示在这些日志中，确认它可以不使用传统的身份验证的用户，实现条件性访问策略或启用**基准策略： 阻止旧式身份验证**仅适用于这些用户。

## <a name="moving-away-from-legacy-authentication"></a>抛弃传统的身份验证

谁在目录中使用传统的身份验证和哪些应用程序依赖于它的更好地了解后下, 一步升级用户能够使用新式身份验证。 新式身份验证是一种提供了更安全的用户身份验证和授权的标识管理的方法。 如果你的目录中的位置有 MFA 策略，可确保新式身份验证，mfa 需要时提示用户。 它是更安全的替代项为旧式身份验证协议。

本部分提供有关如何更新你的环境新式身份验证的分步概述。 启用阻止策略在组织中的旧身份验证之前通读以下步骤。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步骤 1：启用目录中的新式身份验证

启用新式身份验证的第一步确保你的目录支持新式身份验证。 默认情况下，对于或 2017 年 8 月 1 日之后创建的目录启用新式身份验证。 如果你的目录在此日期之前创建的您将需要手动启用新式身份验证为目录使用以下步骤：

1. 如果你的目录已支持新式身份验证，通过运行检查`Get-CsOAuthConfiguration`从[Business Online PowerShell 模块的 Skype](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)。
1. 如果您的命令返回一个空`OAuthServers`属性，则新式身份验证已禁用。 更新的设置以启用新式身份验证使用`Set-CsOAuthConfiguration`。 如果你`OAuthServers`属性包含一个条目，就可以开始。

请确保在继续操作之前完成此步骤。 首先更改目录配置因为规定所有 Office 客户端将都使用哪个协议至关重要。 即使使用的支持新式身份验证的 Office 客户端，但将默认为使用传统协议，如果你的目录上禁用了新式身份验证。

### <a name="step-2-office-applications"></a>步骤 2：Office 应用程序

一旦你的目录中已启用新式身份验证，即可更新通过启用新式身份验证的 Office 客户端应用程序。 Office 2016 或更高版本的客户端默认情况下支持新式身份验证。 没有额外的步骤是必需的。

如果使用的 Office 2013 Windows 客户端或更低版本，我们建议您升级到 Office 2016 或更高版本。 甚至在完成后启用新式身份验证在你的目录中的上一步，较旧的 Office 应用程序将继续使用旧式身份验证协议。 如果您使用的 Office 2013 客户端，并且无法立即升级到 Office 2016 或更高版本，请按照以下文章中的步骤[Office 2013 Windows 设备上启用新式身份验证](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 为了帮助保护你的帐户，在使用传统的身份验证时，我们建议在你的目录中使用强密码。 请查看[Azure AD 密码保护](../authentication/concept-password-ban-bad.md)禁止跨你的目录的弱密码。

Office 2010 不支持新式身份验证。 您需要将任何用户使用 Office 2010 升级到最新版本的 Office。 我们建议升级到 Office 2016 或更高版本，因为它默认情况下阻止旧式身份验证。

如果使用 MacOS，我们建议升级到 Office for Mac 2016 或更高版本。 如果使用的本机邮件客户端，您需要的 MacOS 版本 10.14 或更高版本在所有设备。

### <a name="step-3-exchange-and-sharepoint"></a>步骤 3：Exchange 和 SharePoint

对于使用新式身份验证的基于 Windows 的 Outlook 客户端，Exchange Online 必须也启用新式身份验证。 如果禁用新式身份验证是 Exchange Online、 基于 Windows 的 Outlook 客户端支持新式身份验证 (Outlook 2013 或更高版本) 将使用基本身份验证连接到 Exchange Online 邮箱。

SharePoint Online 启用新式身份验证默认值。 对于 2017 年 8 月 1 日之后创建的目录默认情况下，在 Exchange Online 中启用新式身份验证。 但是，如果以前已禁用新式身份验证，或者是否使用在此日期之前创建的一个目录，按照以下文章中的步骤[Exchange Online 中的启用新式验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步骤 4：Skype for Business

若要防止发出的 Skype for Business 的旧式身份验证请求，就需要启用新式验证的 Skype for Business Online。 对于 2017 年 8 月 1 日之后创建的目录是默认情况下启用新式身份验证的 Skype for Business。

若要启用新式身份验证在 Skype for Business 中的，我们建议在过渡到 Microsoft Teams，默认情况下支持新式身份验证。 但是，如果你不能为 tr 这一次，您需要启用新式验证的 Skype for Business Online，以便 Skype 的业务客户端将开始使用新式身份验证。 一文中执行以下步骤[Skype for Business 拓扑支持新式身份验证](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，有关为 Skype for Business 启用新式身份验证的步骤。

除了启用新式验证的 Skype for Business Online，我们建议新式身份验证为 Exchange Online 启用新式身份验证的 Skype for Business 时启用。 此过程将有助于同步的新式身份验证在 Exchange Online 和 Skype for Business 中联机状态，并将防止多个登录提示的 Skype for Business 客户端。

### <a name="step-5-using-mobile-devices"></a>步骤 5：使用移动设备

你的移动设备上的应用程序需要阻止也旧式身份验证。 我们建议使用适用于移动设备 Outlook。 Outlook Mobile 默认情况下支持新式身份验证和将满足其他 MFA 基线保护策略。

若要使用本机 iOS 邮件客户端，需要运行 iOS 版本 11.0 或更高版本以确保邮件客户端已更新，以阻止旧式身份验证。

### <a name="step-6-on-premises-clients"></a>步骤 6：在本地客户端

如果你是企业内部技术部署中使用本地 Exchange Server 和 Skype 的混合客户，这两个服务将需要更新，以启用新式身份验证。 当在混合环境中使用新式身份验证，您仍正在验证本地用户。 授予对资源 （文件或电子邮件） 更改其访问权限的文章。

你可以开始为用户启用新式身份验证的本地之前，请务必满足 theIf 满足的要求，现在已准备就绪以启用新式身份验证的本地。

启用新式身份验证步骤请参阅以下文章：

* [如何配置 Exchange Server 的本地使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何使用 Skype for Business 使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基准策略：块旧式身份验证 （预览版）** 预配置，此时将显示在顶部导航到在 Azure 门户中的条件性访问边栏选项卡时。

若要启用此策略并保护你的组织：

1. 登录到 **Azure 门户** 作为全局管理员、 安全管理员或条件性访问管理员。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中，选择**基准策略：块旧式身份验证 （预览版）** 。
1. 设置**启用策略**到**立即使用策略**。
1. 通过单击添加任何用户排除项**用户** > **选择排除的用户**，然后选择需要排除的用户。 单击**选择**然后**完成**。
1. 单击 **保存**。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/azure-ad-secure-steps.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
