---
title: 阻止旧身份验证-Azure Active Directory
description: 了解如何通过使用 Azure AD 条件访问阻止旧身份验证来改善安全状况。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380864"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>如何：阻止旧身份验证使用条件性访问 Azure AD   

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 但是，旧协议不支持多重身份验证 (MFA)。 许多环境通常都会要求使用 MFA，以解决身份盗用的情况。 

如果环境已准备好阻止旧身份验证以提高对租户的保护，则可以使用条件访问来实现此目标。 本文介绍如何配置条件访问策略来阻止对租户的旧身份验证。

## <a name="prerequisites"></a>先决条件

本文假定你熟悉以下内容： 

- Azure AD 条件访问的[基本概念](overview.md) 
- 在 Azure 门户中配置条件访问策略的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>方案描述

Azure AD 支持多个最广泛使用的身份验证和授权协议，包括旧身份验证。 旧身份验证是指使用基本身份验证的协议。 通常，这些协议不能强制执行任何类型的第二因素身份验证。 基于旧身份验证的应用程序示例包括：

- 旧版 Microsoft Office 应用
- 使用邮件协议的应用，如 POP、IMAP 和 SMTP

如今，使用单因素身份验证（例如，用户名和密码）还不够安全。 使用密码也不安全，因为它们很容易被猜测到，我们并不擅长选择好密码。 密码也容易受到各种攻击，如网络钓鱼和密码破解。 要防止密码威胁，可以做的最简单的事情之一就是实现 MFA。 使用 MFA，即使攻击者拥有用户密码，仅凭密码也不足以成功验证和访问数据。

如何阻止使用旧身份验证的应用访问租户的资源？ 建议只使用条件访问策略阻止它们。 如有必要，只允许某些用户和特定网络位置使用基于旧身份验证的应用程序。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件访问并不是针对拒绝服务 (DoS) 攻击等情况的第一道防线，而是可以利用来自这些事件的信号（例如，登录风险级别、请求的位置等）来确定访问权限。

## <a name="implementation"></a>实现

本部分介绍如何配置条件访问策略以阻止旧身份验证。 

### <a name="identify-legacy-authentication-use"></a>确定旧身份验证使用情况

需要先了解用户是否有使用旧式身份验证的应用，以及它如何影响整个目录，然后才能在目录中阻止旧式身份验证。 可以使用 Azure AD 登录日志来了解是否正在使用旧式身份验证。

1. 导航到“Azure 门户” > “Azure Active Directory” > “登录”。
1. 单击“列” > “客户端应用”添加“客户端应用”列（如果未显示）。
1. 单击“添加筛选器” > “客户端应用”> 选择“其他客户端”的所有选项，然后单击“应用”。

筛选将仅显示旧式身份验证协议进行的登录尝试。 单击每个单独的登录尝试将显示其他详细信息。 “基本信息”选项卡下的“客户端应用”字段将指示使用了哪个旧式身份验证协议。

这些日志将指示哪些用户仍然依赖于旧身份验证，以及哪些应用程序使用旧协议发出身份验证请求。 对于未出现在这些日志中且已确认不使用旧身份验证的用户，请仅为这些用户实施条件访问策略。

### <a name="block-legacy-authentication"></a>阻止传统身份验证 

在条件访问策略中，可设置与用于访问资源的客户端应用程序绑定的条件。 客户端应用条件使你可以通过为“移动应用和桌面客户端”选择“其他客户端”，将范围缩小到使用旧身份验证的应用程序。

![其他客户端](./media/block-legacy-authentication/01.png)

若要阻止对这些应用的访问，你需要选择“阻止访问”。

![阻止访问](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>选择用户和云应用

如果要阻止组织的旧身份验证，你可能认为可以通过选择以下内容来完成此操作：

- 所有用户
- 所有云应用
- 阻止访问

![分配](./media/block-legacy-authentication/03.png)

Azure 具有一项安全功能，可阻止你创建此类策略，因为此配置违反了条件访问策略的[最佳做法](best-practices.md)。
 
![不支持策略配置](./media/block-legacy-authentication/04.png)

安全功能是必需的，因为“阻止所有用户和所有云应用程序”有可能阻止整个组织向租户注册。 必须至少排除一个用户才能满足最佳做法要求的最低限度。 还可以排除目录角色。

![不支持策略配置](./media/block-legacy-authentication/05.png)

可通过将一个用户排除在策略之外来满足此安全功能。 理想情况下，应定义几个[在 Azure AD 中紧急访问管理帐户](../users-groups-roles/directory-emergency-access.md)，并将其从策略中排除。

## <a name="policy-deployment"></a>策略部署

在将策略投入生产之前，请注意以下几点：
 
- **服务帐户** - 确定用作服务帐户或设备的用户帐户，例如会议室电话。 确保这些帐户具有强密码并将其添加到排除的组。
- **登录报告** - 查看登录报告并查找“其他客户端”流量。 确认最常使用情况，并调查使用原因。 通常，流量由不使用现代身份验证的旧 Office 客户端或某些第三方邮件应用程序生成。 制定计划以将使用情况从这些应用中移除，或者如果影响较小，请通知用户，他们不再能够使用这些应用。
 
有关详细信息，请参阅[应如何部署新策略？](best-practices.md#how-should-you-deploy-a-new-policy)。

## <a name="what-you-should-know"></a>要点

阻止使用**其他客户端**的访问还会阻止 Exchange Online PowerShell 和 Dynamics 365 （使用基本身份验证）。

为“其他客户端”配置策略导致整个组织无法与 SPConnect 之类的特定客户端通信。 之所以发生这种阻止，是因为旧式客户端使用非预期的方式进行身份验证。 此问题不存在于主要的 Office 应用程序（例如旧式 Office 客户端）中。

策略生效可能需要长达 24 小时的时间。

可为**其他客户端**条件选择所有可用的授权控件；但是，最终用户体验始终是相同的 - 阻止访问。

如果使用**其他客户端**条件来阻止旧身份验证，还可以设置设备平台和位置条件。 例如，如果只想阻止移动设备的旧式身份验证，请通过选择以下项来设置**设备平台**条件：

- Android
- iOS
- Windows Phone

![不支持策略配置](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>后续步骤

- 如果你还不熟悉配置条件访问策略，请参见[通过 Azure Active Directory 条件访问要求特定应用进行多重身份验证](app-based-mfa.md)的示例。
- 有关新式身份验证支持的详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
