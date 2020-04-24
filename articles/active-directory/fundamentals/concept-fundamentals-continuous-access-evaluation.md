---
title: Azure AD 中的持续访问评估
description: 使用 Azure AD 中的持续访问评估更快速地响应用户状态的更改
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3713901dd3dd5d17c4e1ddcef529c663b68f5b43
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112569"
---
# <a name="continuous-access-evaluation"></a>连续访问评估

Microsoft 服务（如 Azure Active Directory （Azure AD）和 Office 365）使用开放标准和协议来最大程度地提高互操作性。 最重要的一个是 Open ID Connect （OIDC）。 当客户端应用程序（如 Outlook）连接到 Exchange Online 等服务时，API 请求使用 OAuth 2.0 访问令牌进行授权。 默认情况下，这些访问令牌的有效时间为一小时。 过期后，客户端将重定向回 Azure AD 来刷新它们。 这还可以重新评估用户访问策略-我们可能选择不刷新令牌，原因是条件性访问策略，或者是由于目录中的用户已禁用。 

令牌到期和刷新是行业中的一种标准机制。 也就是说，客户已经对用户的风险条件变化（例如：从企业办公室移动到当地咖啡店，或在黑色市场上发现的用户凭据）之间存在延迟，并且可以强制实施与该更改相关的策略。 我们 vspackage 了降低令牌生存期的 "钝对象" 方法，但发现它们会降低用户体验和可靠性，而不会消除风险。

及时响应策略违规或安全问题时，在令牌颁发者（如 Azure AD）和信赖方（如 Exchange Online）之间需要 "会话"。 此双向会话提供了两项重要功能。 依赖方可以在发生更改时（例如，客户端来自新位置）通知，并通知令牌颁发者。 它还为令牌颁发者提供一种方法，告知依赖方出于帐户泄漏、禁用或其他考虑而停止尊重给定用户的令牌。 此会话的机制是持续访问评估（CAE）。

Microsoft 已成为持续访问评估协议（CAEP）计划中的初期参与者，作为在 OpenID Foundation 上[共享信号和事件](https://openid.net/wg/sse/)工作组的一部分。 标识提供者和信赖方将能够利用由工作组定义的安全事件和信号来重新授权或终止访问。 这是一种令人兴奋的工作，可以提高许多平台和应用程序的安全性。

由于安全优势非常好，我们将在标准机构中并行推出一种 Microsoft 特定的初始实现，以实现持续工作。 随着我们努力在 Microsoft 服务中部署这些连续访问评估（CAE）功能，我们已经了解了很多知识，并与标准社区共享了此信息。 我们希望我们的部署经验有助于通知更好的行业标准，并承诺在批准后实现该标准，允许所有参与的服务受益。

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE 在 Microsoft 服务中的工作原理是什么？

我们将重点介绍如何实现对 Exchange 和团队的连续访问评估。 我们希望将来将支持扩展到其他 Microsoft 服务。 我们将开始为没有条件性访问策略的租户启用持续访问评估。 我们将从 CAE 的这一阶段使用我们的知识来通知我们正在推出的 CAE。

## <a name="service-side-requirements"></a>服务端要求

可以通过使服务（资源提供程序）订阅 Azure AD 中的关键事件来实现持续性访问评估，以便能够以接近实时的方式评估和强制执行这些事件。 此初始 CAE 推出将强制实施以下事件：

- 用户帐户已被删除或禁用
- 用户的密码已更改或已重置
- 管理员显式撤销用户的所有刷新令牌
- Azure AD Identity Protection 检测到提升的用户风险

将来，我们希望添加更多事件，包括诸如位置和设备状态更改之类的事件。 **尽管我们的目标是使强制成为即时的，但在某些情况下，可能会由于事件传播时间而导致最多15分钟的延迟**。 

## <a name="client-side-claim-challenge"></a>客户端声明质询

在持续访问评估之前，客户端将始终尝试从其缓存中重播访问令牌，只要该令牌未过期。 通过 CAE，我们引入了一个新的案例，即资源提供程序即使在未过期的情况下也可以拒绝令牌。 为了通知客户端即使缓存的令牌未过期，也会绕过缓存，我们引入了一种称为 "**声明质询**" 的机制。 CAE 要求客户端更新以了解索赔质询。 以下应用程序的最新版本支持声明质询：

- 适用于 Windows 的 Outlook 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- 适用于 Windows 的团队
- 团队 iOS 
- 团队 Android 
- 团队 Mac 

## <a name="token-lifetime"></a>令牌生存期

由于风险和策略是实时评估的，因此协商持续访问评估识别会话的客户端将依赖于 CAE，而不是现有的静态访问令牌生存期策略，这意味着，对于协商 CAE 识别会话的支持 CAE 的客户端，可配置的令牌生存期策略将不再有效。

我们会在 CAE 会话中将访问令牌生存期增加到24小时。 吊销由关键事件和策略评估驱动，而不是任意时间段。 此更改会提高应用程序的稳定性，而不会影响安全状况。 

## <a name="example-flows"></a>示例流

### <a name="user-revocation-event-flow"></a>用户吊销事件流：

![用户吊销事件流](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支持 CAE 的客户端向 AAD 提供凭据或刷新令牌，请求某个资源的访问令牌。
1. 访问令牌与其他项目一起返回到客户端。
1. 管理员显式[撤销用户的所有刷新令牌](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 吊销事件将从 Azure AD 发送到资源提供程序。
1. 向资源提供程序提供一个访问令牌。 资源提供程序评估令牌的有效性，并检查用户是否有任何吊销事件。 资源提供程序使用此信息来决定是否授予对资源的访问权限。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401 + 声明质询发送回客户端
1. 支持 CAE 的客户端了解 401 + 声明质询。 它会绕过缓存并返回到步骤1，将其刷新令牌连同声明质询一起发送回 Azure AD。 然后 Azure AD 会重新评估所有条件，并在这种情况下提示用户重新进行身份验证。
 
## <a name="faqs"></a>常见问题解答

### <a name="what-is-the-lifetime-of-my-access-token"></a>我的访问令牌的生存期是多少？

如果不使用支持 CAE 的客户端，则默认访问令牌生存期仍然为1小时，除非已使用[可配置的令牌生存期（CTL）](../develop/active-directory-configurable-token-lifetimes.md)预览功能配置了访问令牌生存期。

如果你使用的是支持 CAE 的客户端，并且这些客户端协商了 CAE 感知会话，则将覆盖你的访问令牌生存期的 CTL 设置，并且访问令牌生存期将为24小时。

### <a name="how-quick-is-enforcement"></a>强制执行速度如何？

尽管我们的目标是使强制成为即时的，但在某些情况下，可能会由于事件传播时间而导致最多15分钟的延迟。

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE 的工作原理是什么？

登录频率将符合或不包含 CAE。

## <a name="next-steps"></a>后续步骤

[宣布持续访问评估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
