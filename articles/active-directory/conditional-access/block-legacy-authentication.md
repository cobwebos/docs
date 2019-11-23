---
title: Block legacy authentication - Azure Active Directory
description: Learn how to improve your security posture by blocking legacy authentication using Azure AD Conditional Access.
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
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>How to: Block legacy authentication to Azure AD with Conditional Access   

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 但是，旧协议不支持多重身份验证 (MFA)。 许多环境通常都会要求使用 MFA，以解决身份盗用的情况。 

If your environment is ready to block legacy authentication to improve your tenant's protection, you can accomplish this goal with Conditional Access. This article explains how you can configure Conditional Access policies that block legacy authentication for your tenant.

## <a name="prerequisites"></a>必备组件

本文假定你熟悉以下内容： 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>方案描述

Azure AD 支持多个最广泛使用的身份验证和授权协议，包括旧身份验证。 旧身份验证是指使用基本身份验证的协议。 通常，这些协议不能强制执行任何类型的第二因素身份验证。 基于旧身份验证的应用程序示例包括：

- 旧版 Microsoft Office 应用
- 使用邮件协议的应用，如 POP、IMAP 和 SMTP

如今，使用单因素身份验证（例如，用户名和密码）还不够安全。 使用密码也不安全，因为它们很容易被猜测到，我们并不擅长选择好密码。 密码也容易受到各种攻击，如网络钓鱼和密码破解。 要防止密码威胁，可以做的最简单的事情之一就是实现 MFA。 使用 MFA，即使攻击者拥有用户密码，仅凭密码也不足以成功验证和访问数据。

如何阻止使用旧身份验证的应用访问租户的资源？ The recommendation is to just block them with a Conditional Access policy. 如有必要，只允许某些用户和特定网络位置使用基于旧身份验证的应用程序。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件访问并不是针对拒绝服务 (DoS) 攻击等情况的第一道防线，而是可以利用来自这些事件的信号（例如，登录风险级别、请求的位置等）来确定访问权限。

## <a name="implementation"></a>实现

This section explains how to configure a Conditional Access policy to block legacy authentication. 

### <a name="identify-legacy-authentication-use"></a>Identify legacy authentication use

Before you can block legacy authentication in your directory, you need to first understand if your users have apps that use legacy authentication and how it affects your overall directory. Azure AD sign-in logs can be used to understand if you’re using legacy authentication.

1. Navigate to the **Azure portal** > **Azure Active Directory** > **Sign-ins**.
1. Add the Client App column if it is not shown by clicking on **Columns** > **Client App**.
1. **Add filters** > **Client App** > select all of the options for **Other clients** and click **Apply**.

Filtering will only show you sign-in attempts that were made by legacy authentication protocols. Clicking on each individual sign-in attempt will show you additional details. The **Client App** field under the **Basic Info** tab will indicate which legacy authentication protocol was used.

These logs will indicate which users are still depending on legacy authentication and which applications are using legacy protocols to make authentication requests. For users that do not appear in these logs and are confirmed to not be using legacy authentication, implement a Conditional Access policy for these users only.

### <a name="block-legacy-authentication"></a>阻止传统身份验证 

In a Conditional Access policy, you can set a condition that is tied to the client apps that are used to access your resources. 客户端应用条件使你可以通过为“移动应用和桌面客户端”选择“其他客户端”，将范围缩小到使用旧身份验证的应用程序。

![其他客户端](./media/block-legacy-authentication/01.png)

若要阻止对这些应用的访问，你需要选择“阻止访问”。

![阻止访问](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>选择用户和云应用

如果要阻止组织的旧身份验证，你可能认为可以通过选择以下内容来完成此操作：

- 所有用户
- 所有云应用
- 阻止访问

![分配](./media/block-legacy-authentication/03.png)

Azure has a safety feature that prevents you from creating a policy like this because this configuration violates the  [best practices](best-practices.md) for Conditional Access policies.
 
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

Blocking access using **Other clients** also blocks Exchange Online PowerShell and Dynamics 365 using basic auth.

为“其他客户端”配置策略导致整个组织无法与 SPConnect 之类的特定客户端通信。 之所以发生这种阻止，是因为旧式客户端使用非预期的方式进行身份验证。 此问题不存在于主要的 Office 应用程序（例如旧式 Office 客户端）中。

策略生效可能需要长达 24 小时的时间。

You can select all available grant controls for the **Other clients** condition; however, the end-user experience is always the same - blocked access.

If you block legacy authentication using the **Other clients** condition, you can also set the device platform and location condition. 例如，如果只想阻止移动设备的旧式身份验证，请通过选择以下项来设置**设备平台**条件：

- Android
- iOS
- Windows Phone

![不支持策略配置](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>后续步骤

- If you are not familiar with configuring Conditional Access policies yet, see [require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) for an example.
- For more information about modern authentication support, see [How modern authentication works for Office 2013 and Office 2016 client apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
