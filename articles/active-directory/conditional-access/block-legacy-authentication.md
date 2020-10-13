---
title: 阻止旧式身份验证 - Azure Active Directory
description: 了解如何通过使用 Azure AD 条件访问阻止旧式身份验证来改善安全状况。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca9f4e290c5dad45e5bf87439ebcd1c88a7c540f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601997"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>如何：使用条件访问阻止向 Azure AD 进行旧身份验证   

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 但是，旧协议不支持多重身份验证 (MFA)。 许多环境通常都会要求使用 MFA，以解决身份盗用的情况。 

Microsoft 身份安全主管 Alex Weinert 在其 2020 年 3 月 12 日的博客文章 [New tools to block legacy authentication in your organization](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#)（阻止组织中旧式身份验证的新工具）中强调了为什么组织应该阻止旧式身份验证，以及 Microsoft 提供了哪些其他工具来完成此任务：

> 要使 MFA 生效，还需要阻止旧式身份验证。 这是因为旧式身份验证协议（例如 POP、SMTP、IMAP 和 MAPI）不能强制实施 MFA，这使其成为攻击者对组织发起进攻的首选入口点。
> 
>对 Azure Active Directory (Azure AD) 流量的分析表明，有关旧式身份验证的数字非常严峻：
> 
> - 超过 99% 的密码喷射攻击使用旧式身份验证协议
> - 超过 97% 的凭据填充攻击使用旧式身份验证
> - 与启用了旧式身份验证的组织相比，在禁用了旧式统身份验证的组织中，Azure AD 帐户受到的危害降低了 67%
>

如果环境已准备好阻止旧式身份验证以提高对租户的保护，则可以使用条件访问来实现此目标。 本文介绍如何配置条件访问策略来阻止对租户的旧身份验证。

## <a name="prerequisites"></a>先决条件

本文假定你熟悉以下内容： 

- Azure AD 条件访问的[基本概念](overview.md) 
- 在 Azure 门户中配置条件访问策略的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>方案描述

Azure AD 支持多个最广泛使用的身份验证和授权协议，包括旧身份验证。 旧身份验证是指使用基本身份验证的协议。 通常，这些协议不能强制执行任何类型的第二因素身份验证。 基于旧身份验证的应用程序示例包括：

- 旧版 Microsoft Office 应用
- 使用邮件协议的应用，如 POP、IMAP 和 SMTP

如今，使用单因素身份验证（例如，用户名和密码）还不够安全。 使用密码也不安全，因为它们很容易被猜测到，我们并不擅长选择好密码。 密码也容易受到各种攻击，如网络钓鱼和密码破解。 防范密码威胁的最简单措施之一是实现多重身份验证 (MFA)。 使用 MFA，即使攻击者拥有用户密码，仅凭密码也不足以成功验证和访问数据。

如何阻止使用旧身份验证的应用访问租户的资源？ 建议只使用条件访问策略阻止它们。 如有必要，只允许某些用户和特定网络位置使用基于旧身份验证的应用程序。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件访问并不是针对拒绝服务 (DoS) 攻击等情况的第一道防线，而是可以利用来自这些事件的信号（例如，登录风险级别、请求的位置等）来确定访问权限。

## <a name="implementation"></a>实现

本节介绍如何配置条件访问策略以阻止旧式身份验证。 

### <a name="legacy-authentication-protocols"></a>旧式身份验证协议

以下选项被视为旧身份验证协议

- 经身份验证的 SMTP - 由 POP 和 IMAP 客户端用来发送电子邮件。
- 自动发现 - 由 Outlook 和 EAS 客户端用来查找和连接 Exchange Online 中的邮箱。
- Exchange ActiveSync (EAS) - 用于连接到 Exchange Online 中的邮箱。
- Exchange Online PowerShell - 用于通过远程 PowerShell 连接到 Exchange Online。 如果阻止 Exchange Online PowerShell 的基本身份验证，则需使用 Exchange Online PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到 Exchange Online PowerShell](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
- Exchange Web 服务 (EWS) - Outlook、Outlook for Mac 和第三方应用使用的编程接口。
- IMAP4 - 由 IMAP 电子邮件客户端使用。
- 基于 HTTP 的 MAPI (MAPI/HTTP) - 由 Outlook 2010 及更高版本使用。
- 脱机通讯簿 (OAB) - 通过 Outlook 下载并使用的地址列表集合的副本。
- Outlook Anywhere（基于 HTTP 的 RPC）- 由 Outlook 2016 及更低版本使用。
- Outlook 服务 - 由 Windows 10 的邮件和日历应用使用。
- POP3 - 由 POP 电子邮件客户端使用。
- Reporting Web Services - 用于在 Exchange Online 中检索报表数据。
- 其他客户端 - 标识为使用旧式身份验证的其他协议。

有关这些身份验证协议和服务的详细信息，请参阅 [Azure Active Directory 门户中的登录活动报告](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)。

### <a name="identify-legacy-authentication-use"></a>识别旧式身份验证的用法

在目录中阻止旧式身份验证之前，需要先了解用户是否有使用旧式身份验证的应用，以及它如何影响整个目录。 可以使用 Azure AD 登录日志来了解是否正在使用旧式身份验证。

1. 导航到“Azure 门户” > “Azure Active Directory” > “登录”。  
1. 如果未显示“客户端应用”列，请单击“列” > “客户端应用”添加该列。 
1. “添加筛选器” > “客户端应用”> 选择所有旧式身份验证协议 。 在筛选对话框外选择，以应用所选项并关闭该对话框。

筛选将仅显示通过旧式身份验证协议进行的登录尝试。 单击每个单独的登录尝试将显示其他详细信息。 “基本信息”选项卡下的“客户端应用”字段将指示使用了哪个旧式身份验证协议。

这些日志将指示哪些用户仍然依赖于旧身份验证，以及哪些应用程序使用旧协议发出身份验证请求。 对于未出现在这些日志中且已确认不使用旧身份验证的用户，请仅为这些用户实施条件访问策略。

## <a name="block-legacy-authentication"></a>阻止传统身份验证 

使用条件访问策略来阻止旧式身份验证的方式有两种。

- [直接阻止旧式身份验证](#directly-blocking-legacy-authentication)
- [间接阻止旧式身份验证](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>直接阻止旧式身份验证

在整个组织中阻止旧式身份验证的最简单方法是配置条件访问策略，该策略专门应用于旧式身份验证客户端并阻止访问。 在将用户和应用程序分配到该策略时，请确保排除仍需使用旧式身份验证进行登录的用户和服务帐户。 请通过选择“Exchange ActiveSync 客户端”和“其他客户端”来配置客户端应用条件 。 若要阻止对这些客户端应用的访问，请将访问控制配置为“阻止访问”。

![配置为阻止旧式身份验证的客户端应用条件](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>间接阻止旧式身份验证

即使组织尚未准备好在整个组织中阻止旧式身份验证，也应确保使用旧式身份验证的登录不会绕开要求授权控制（例如要求多重身份验证或合规/已加入混合 Azure AD 的设备）的策略。 在身份验证过程中，旧式身份验证客户端不支持将 MFA、设备合规性或加入状态信息发送到 Azure AD。 因此，请将具有授权控制的策略应用于所有客户端应用程序，以便阻止无法满足授权控制要求的基于旧式身份验证的登录。 随着客户端应用条件在 2020 年 8 月正式发布，新创建的条件访问策略会默认应用于所有客户端应用。

![客户端应用条件默认配置](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>要点

阻止使用其他客户端的访问也会阻止使用基本身份验证的 Exchange Online PowerShell 和 Dynamics 365。

为“其他客户端”配置策略导致整个组织无法与 SPConnect 之类的特定客户端通信。 之所以发生这种阻止，是因为旧式客户端使用非预期的方式进行身份验证。 此问题不存在于主要的 Office 应用程序（例如旧式 Office 客户端）中。

策略生效可能需要长达 24 小时的时间。

可为**其他客户端**条件选择所有可用的授权控件；但是，最终用户体验始终是相同的 - 阻止访问。

## <a name="next-steps"></a>后续步骤

- [使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)
- 如果你还不熟悉配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用进行多重身份验证 (MFA)](../authentication/tutorial-enable-azure-mfa.md) 的示例。
- 有关新式身份验证支持的详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [如何使用 Microsoft 365 设置多功能设备或应用程序以发送电子邮件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
