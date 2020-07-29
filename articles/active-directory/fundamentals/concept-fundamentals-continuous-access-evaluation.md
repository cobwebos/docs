---
title: Azure AD 中的连续访问评估
description: 使用 Azure AD 中的连续访问评估，更快地响应用户状态的更改
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
ms.openlocfilehash: f0cb402741163c657b3e7961eb5a4f9c8e18dafd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673014"
---
# <a name="continuous-access-evaluation"></a>连续访问评估

Azure Active Directory (Azure AD) 和 Office 365 等 Microsoft 服务使用开放标准和协议来最大限度地提高互操作性。 其中最关键的一个是 OpenID Connect (OIDC)。 当客户端应用程序（如 Outlook）连接到服务（如 Exchange Online）时，API 请求通过 OAuth 2.0 访问令牌得到授权。 默认情况下，这些访问令牌的有效期为一小时。 过期后，客户端将重定向回 Azure AD 以刷新它们。 这还可以重新评估用户访问策略-我们可能选择不刷新令牌，原因是条件性访问策略，或者是由于目录中的用户已禁用。 

令牌过期和刷新是业界的一种标准机制。 也就是说，客户已经对用户的风险条件发生变化（例如：从公司办公室转移到当地咖啡店，或者在黑市上发现用户凭证）的时间与可执行与该变化相关的策略的时间之间的滞后表达了担忧。 我们已经尝试了降低令牌寿命的生硬方法，但发现这种方法会降低用户体验和可靠性，而不会消除风险。

对策略冲突或安全问题的及时响应实际上需要令牌颁发者（如 Azure AD）和依赖方（如 Exchange Online）之间进行“对话”。 这种双向对话提供了两项重要功能。 信赖方可以注意到事情的变化（比如客户端来自一个新的位置），并通知令牌颁发者。 通过此对话，令牌颁发者也可通知信赖方由于帐户泄露、禁用或其他问题而停止遵从给定用户的令牌。 此对话的机制是连续访问评估 (CAE)。

连续访问评估协议 (CAEP) 计划属于 OpenID Foundation [共享信号和事件](https://openid.net/wg/sse/)工作组的一部分，而 Microsoft 是这项计划的早期参与者。 标识提供者和信赖方将能够利用工作组定义的安全事件和信号来重新授权或终止访问。 这项工作非常激动人心，可提高许多平台和应用程序的安全性。

由于安全方面的优势如此突出，因此我们在标准组织内继续开展工作的同时，将推出一个特定于 Microsoft 的初始实现。 在我们跨 Microsoft 服务部署这些连续访问评估 (CAE) 功能时，我们学到了很多知识，并与标准社区共享这些信息。 我们希望我们在部署方面的经验有助于制定一个更好的行业标准，并且我们承诺一旦该标准获得批准就予以实施，让所有参与的服务都能从中受益。

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE 在 Microsoft 服务中是如何工作的？

我们将重点放在对 Exchange 和 Teams 的持续访问评估的初始实现上。 我们希望在未来能扩展对其他 Microsoft 服务的支持。 我们开始仅对没有条件访问策略的租户启用持续访问评估。 我们将利用在 CAE 这一阶段所学到的知识，为我们正在进行的 CAE 实施提供信息。

## <a name="service-side-requirements"></a>服务端要求

通过允许服务（资源提供程序）订阅 Azure AD 中的关键事件来实现连续访问评估，从而可以准实时地评估和强制实施这些事件。 以下事件将在最初的 CAE 实施中强制执行：

- 用户帐户已删除或禁用
- 用户的密码已更改或已重置
- 已为用户启用 MFA
- 管理员显式地撤销用户的所有刷新令牌
- Azure AD Identity Protection 检测到提升的用户风险

将来我们希望添加更多事件，包括位置和设备状态更改等事件。 虽然我们的目标是即时强制执行，但在某些情况下，由于事件传播时间的原因，延迟可能会长达 15 分钟。 

## <a name="client-side-claim-challenge"></a>客户端声明质询

在进行连续访问评估之前，只要访问令牌未过期，客户端将始终试图从其缓存中重播访问令牌。 通过使用 CAE，我们引入了一种新的事例 - 即使令牌没有过期，资源提供程序也可以拒绝令牌。 为了在缓存的令牌尚未过期的情况下通知客户端绕过其缓存，我们引入了一种称为“声明质询”的机制。 CAE 要求客户端更新以理解声明质询。 以下应用程序的最新版本支持声明质询：

- 适用于 Windows 的 Outlook 
- 适用于 iOS 的 Outlook 
- 适用于 Android 的 Outlook 
- Outlook for Mac 
- 适用于 Windows 的 Teams
- IOS 团队 
- 适用于 Android 的团队 
- 适用于 Mac 的团队 

## <a name="token-lifetime"></a>令牌生存期

由于风险和策略是实时评估的，协商连续访问评估感知会话的客户端将依赖于 CAE，而不是现有的静态访问令牌生存期策略，这意味着协商 CAE 感知会话的支持 CAE 的客户端将不再遵守可配置令牌生存期策略。

在 CAE 会话中，我们将访问令牌生存期增加到 24 小时。 吊销是由关键事件和策略评估驱动的，而不是任意的时间段。 此更改可提高应用程序的稳定性，而不会影响安全状况。 

## <a name="example-flows"></a>示例流

### <a name="user-revocation-event-flow"></a>用户吊销事件流：

![用户吊销事件流](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支持 CAE 的客户端向 AAD 提供凭据或刷新令牌，要求获得某个资源的访问令牌。
1. 访问令牌与其他项目一起返回到客户端。
1. 管理员显式地[撤销用户的所有刷新令牌](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 吊销事件将从 Azure AD 发送到资源提供程序。
1. 向资源提供程序提供访问令牌。 资源提供程序评估令牌的有效性，并检查用户是否存在任何吊销事件。 资源提供程序使用此信息来决定是否授予对资源的访问权限。
1. 在这种情况下，资源提供程序会拒绝访问，并将 401+ 声明质询发送回客户端
1. 支持 CAE 的客户端理解 401+ 声明质询。 它绕过缓存并返回到步骤 1，将其刷新令牌和声明质询一起发送回 Azure AD。 然后在此情况下，Azure AD 将重新评估所有条件，并提示用户重新进行身份验证。

## <a name="faqs"></a>常见问题

### <a name="what-is-the-lifetime-of-my-access-token"></a>我的访问令牌的生存期是多少？

如果不使用支持 CAE 的客户端，则默认访问令牌生存期仍然为1小时，除非已使用[可配置的令牌生存期（CTL）](../develop/active-directory-configurable-token-lifetimes.md)预览功能配置了访问令牌生存期。

如果你使用可协商 CAE 感知会话的支持 CAE 的客户端，则访问令牌生存期的 CTL 设置将被覆盖，访问令牌生存期将为 24 小时。

### <a name="how-quick-is-enforcement"></a>强制执行速度如何？

虽然我们的目标是即时强制执行，但在某些情况下，由于事件传播时间的原因，延迟可能会长达 15 分钟。

### <a name="how-will-cae-work-with-sign-in-frequency"></a>如何将 CAE 与登录频率一起使用？

无论是否使用 CAE，登录频率都会得到遵循。

## <a name="next-steps"></a>后续步骤

[宣布连续访问评估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
