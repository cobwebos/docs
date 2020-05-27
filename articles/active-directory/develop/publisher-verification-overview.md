---
title: 发布者验证概述 - Microsoft 标识平台 | Azure
description: 概要介绍了 Microsoft 标识平台的发布者验证计划（预览版）。 列出优势、计划要求和常见问题。 当应用程序标记为“发布者已验证”时，表示发布者已使用 Microsoft 合作伙伴网络帐户（该帐户已完成验证过程）验证了身份，并将 MPN 帐户与其应用程序注册相关联。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 73a96f295d5dfa74130927e5096e9278a0e348e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682429"
---
# <a name="publisher-verification-preview"></a>发布者验证（预览版）

发布者验证（预览版）旨在帮助管理员和最终用户了解与 Microsoft 标识平台集成的应用程序开发人员的真实身份。 当应用程序标记为“发布者已验证”时，表示发布者已使用 [Microsoft 合作伙伴网络](https://partner.microsoft.com/membership)帐户（该帐户已完成[验证](/partner-center/verification-responses)过程）验证了身份，并将 MPN 帐户与其应用程序注册相关联。 

蓝色的“已验证”徽章会显示在 Azure AD 同意提示和其他屏幕上：![同意提示](./media/publisher-verification-overview/consent-prompt.png)

此功能主要是为构建多租户应用的开发人员提供的，这些应用利用了 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md) 及 [Microsoft 标识平台](v2-overview.md)。 这些应用可以使用 OpenID Connect 来让用户登录，或者他们可以使用 OAuth 2.0 来请求访问使用 API（类似 [Microsoft Graph](https://developer.microsoft.com/graph/)）的数据。

## <a name="benefits"></a>优点
发布者验证具有以下优势：
- 为客户增加透明度和降低风险此功能可帮助客户了解其组织中使用的哪些应用是由他们信任的开发人员发布的。 

- 提升品牌形象-“已验证”徽章会出现在 Azure AD [同意提示](application-consent-experience.md)、企业应用页以及最终用户和管理员使用的其他 UX 设计面上。 

- 推动企业更顺畅地采用- 管理员可以配置新的用户同意策略，发布者验证状态将是主要的策略标准之一。 

- 改进风险评估- 发布者验证将作为 Microsoft 检测“有风险的”的同意请求的一个信号。 

## <a name="requirements"></a>要求
发布者验证需要满足一些先决条件，许多 Microsoft 合作伙伴已经达成了其中一些条件。 它们分别是： 

-  一个有效的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/membership)帐户的 MPN ID，该帐户已完成[验证](/partner-center/verification-responses)过程。 这个 MPN 帐户必须是贵组织的[合作伙伴全局帐户 (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga)。 

-  一个 Azure AD 租户，具有经 DNS 验证的[自定义域](/azure/active-directory/fundamentals/add-custom-domain)。 自定义域必须与上一步中验证时使用的电子邮件地址的域相匹配。 

-  一个在 Azure AD 租户中注册的应用，使用之前使用的相同域配置了[发布者域](howto-configure-publisher-domain.md)。 

-  执行验证的用户必须获得授权，才能对 Azure AD 中的应用注册和合作伙伴中心中的 MPN 帐户进行更改。 

    -  在 Azure AD 中，此用户必须是应用的所有者或拥有以下[角色](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)之一。应用程序管理员、云应用程序管理员和全局管理员。 

    -  在合作伙伴中心，该用户必须拥有以下[角色](/partner-center/permissions-overview)之一。MPN 管理员、帐户管理员或全局管理员（这是 Azure AD 中主导的共享角色）。
    
-  发布者同意[面向开发人员的 Microsoft 标识平台使用条款](/legal/microsoft-identity-platform/terms-of-use)。

已经满足这些先决条件的开发人员可以在几分钟内完成验证。 如果未满足要求，则可以免费进行设置。 

## <a name="frequently-asked-questions"></a>常见问题 
以下是有关发布者验证计划的一些常见问题。 有关要求和流程的常见问题解答，请参阅[将应用标记为“发布者已验证”](mark-app-as-publisher-verified.md)。

- 发布者验证不提供哪些信息？  当应用程序标记为“发布者已验证”时，并不表明应用程序或其发布者已获得任何特定的认证、符合行业标准、遵守最佳实践等。其他 Microsoft 计划确实提供了此信息，包括 [Microsoft 365 应用认证](/microsoft-365-app-certification/overview)。

- 此项花费有多大？是否需要许可证？ Microsoft 不向开发人员收取发布者验证费用，并且不需要任何特定的许可证。 

- 这与 Microsoft 365 发布者证明有什么关系？与 Microsoft 365 应用认证有什么关系？ 这些是补充计划，开发人员可以使用它们来创建值得信赖的应用，以便客户可以放心使用。 发布者验证是此过程的第一步，所有创建应用且符合上述条件的开发人员都应完成发布者验证。 

  此外，与 Microsoft 365 集成的开发人员可以从这些计划获得更多好处。 有关详细信息，请参阅 [ Microsoft 365 发布者证明](/microsoft-365-app-certification/docs/attestation)和 [Microsoft 365 应用认证](/microsoft-365-app-certification/docs/certification)。 

- 这与 Azure AD 应用程序库一样吗？ 不一样。无发布者验证是一项独立计划，是对 [Azure Active Directory 应用程序库](/azure/active-directory/azuread-dev/howto-app-gallery-listing)的补充。 符合上述标准的开发人员应完成发布者验证过程（与参与该计划无关）。 

## <a name="next-steps"></a>后续步骤
* 了解如何[将应用标记为“发布者已验证”](mark-app-as-publisher-verified.md)。
* [排查](troubleshoot-publisher-verification.md)发布者验证问题。