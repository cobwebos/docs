---
title: Azure AD 中的连续访问评估
description: 通过 Azure AD 中的连续访问评估更快地响应用户状态更改
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
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873298"
---
# <a name="continuous-access-evaluation"></a>连续访问评估

Microsoft 服务（如 Azure 活动目录 （Azure AD） 和 Office 365）使用开放标准和协议来最大化互操作性。 其中最关键的是打开 ID 连接 （OIDC）。 当 Outlook 等客户端应用程序连接到 Exchange Online 等服务时，API 请求将使用 OAuth 2.0 访问令牌进行授权。 默认情况下，这些访问令牌的有效期为一小时。 当客户端过期时，客户端将重定向回 Azure AD 以刷新它们。 这也提供了重新评估用户访问策略的机会 - 我们可能选择不刷新令牌，因为条件访问策略，或者因为用户已在目录中禁用。 

我们听到来自客户的大量反馈：由于重新应用条件访问策略的访问令牌生存期导致一小时的延迟，以及用户状态的更改（例如：由于毛茸茸而禁用）不够好。

作为 OpenID 基金会[共享信号和事件](https://openid.net/wg/sse/)工作组的一部分，Microsoft 已提前参加了连续访问评估协议 （CAEP） 计划。 标识提供程序和依赖方将能够利用工作组定义的安全事件和信号重新授权或终止访问。 这是一个令人兴奋的工作，将提高许多平台和应用程序的安全性。

由于安全优势如此之大，我们正在推出特定于 Microsoft 的初始实现，同时我们在标准机构内继续工作。 当我们努力跨 Microsoft 服务部署这些连续访问评估 （CAE） 功能时，我们学到了很多东西，并且正在与标准社区共享此信息。 我们希望我们的部署经验能够有助于制定更好的行业标准，并承诺一旦批准该标准，即可实施该标准，从而使所有参与服务受益。

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE 在 Microsoft 服务中是如何工作的？

我们正在集中对 Exchange 和团队进行持续访问评估的初始实施。 我们希望将来能扩大对微软其他服务的支持。 我们将开始仅对没有条件访问策略的租户启用连续访问评估。 我们将利用我们在此阶段从 CAE 中学到的知识，为正在进行的 CAE 推出提供信息。

## <a name="service-side-requirements"></a>服务方要求

通过启用服务（资源提供程序）订阅 Azure AD 中的关键事件，以便可以近乎实时地评估和强制执行这些事件，从而实现连续访问评估。 在此初始 CAE 推出中将强制执行以下事件：

- 用户帐户被删除或禁用
- 更改或重置用户的密码
- 管理员显式撤消用户的所有刷新令牌
- Azure AD 标识保护检测到的升高的用户风险

将来，我们希望添加更多事件，包括位置和设备状态更改等事件。 **虽然我们的目标是强制是即时的，但在某些情况下，由于事件传播时间，可能会观察到长达 15 分钟的延迟**。 

## <a name="client-side-claim-challenge"></a>客户端索赔挑战

在连续访问评估之前，只要访问令牌未过期，客户端始终会尝试从缓存中重播访问令牌。 使用 CAE，我们引入了一个新案例，即资源提供程序可以拒绝令牌，即使令牌未过期也是如此。 为了通知客户端绕过缓存，即使缓存的令牌尚未过期，我们引入了一种称为**声明质询**的机制。 CAE 需要客户端更新才能了解索赔挑战。 以下支持索赔挑战的最新版本：

- 视窗展望 
- Outlook iOS 
- 展望安卓 
- 展望 Mac 
- 窗口团队
- 团队 iOS 
- 团队安卓 
- 团队 Mac 

## <a name="token-lifetime"></a>令牌生存期

由于风险和策略是实时评估的，因此协商连续访问评估感知会话的客户端将依赖于 CAE 而不是现有的静态访问令牌生存期策略，这意味着协商 CAE 感知会话的启用 CAE 的客户端将不再遵守可配置的令牌生存期策略。

我们将在 CAE 会话中将访问令牌生存期增加到 24 小时。 吊销是由关键事件和策略评估驱动的，而不是任意的时间段。 此更改提高了应用程序的稳定性，而不会影响您的安全状态。 

## <a name="example-flows"></a>示例流

### <a name="user-revocation-event-flow"></a>用户吊销事件流：

![用户吊销事件流](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. 支持 CAE 的客户端向 AAD 提供凭据或刷新令牌，要求为某些资源请求访问令牌。
1. 访问令牌与其他项目一起返回到客户端。
1. 管理员显式[撤消用户的所有刷新令牌](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 吊销事件将从 Azure AD 发送到资源提供程序。
1. 向资源提供程序显示访问令牌。 资源提供程序评估令牌的有效性，并检查用户是否有任何吊销事件。 资源提供程序使用此信息决定是否授予对资源的访问权限。
1. 在这种情况下，资源提供程序拒绝访问，并将 401+ 索赔质询发送回客户端
1. 支持 CAE 的客户端了解 401+ 索赔挑战。 它绕过缓存并返回步骤 1，将其刷新令牌以及声明质询一起发送回 Azure AD。 然后，Azure AD 将重新评估所有条件，并提示用户在这种情况下重新进行身份验证。
 
## <a name="faqs"></a>常见问题解答

### <a name="what-is-the-lifetime-of-my-access-token"></a>我的访问令牌的生存期是多少？

如果您没有使用支持 CAE 的客户端，则默认访问令牌生存期仍为 1 小时，除非您已使用[可配置令牌生存期 （CTL）](../develop/active-directory-configurable-token-lifetimes.md)预览功能配置了访问令牌生存期。

如果您使用的是支持 CAE 的客户端协商 CAE 感知会话，则访问令牌生存期的 CTL 设置将被覆盖，访问令牌生存期为 24 小时。

### <a name="how-quick-is-enforcement"></a>执行速度有多快？

虽然我们的目标是实现即时执行，但在某些情况下，由于事件传播时间，可能会观察到长达 15 分钟的延迟。

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE 如何使用登录频率？

登录频率将遵循或没有 CAE。

## <a name="next-steps"></a>后续步骤

[宣布连续访问评估](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
