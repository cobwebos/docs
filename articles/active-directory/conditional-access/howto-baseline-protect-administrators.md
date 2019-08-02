---
title: 基线策略需要针对管理员的 MFA-Azure Active Directory
description: 要求管理员进行多重身份验证的条件性访问策略
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
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608159"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>基准策略:要求对管理员进行 MFA (预览)

对特权帐户具有访问权限的用户对你的环境具有不受限制的访问权限。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 增强对特权帐户的保护的一种常用方法是要求在使用这些帐户登录时进行更强的帐户验证。 在 Azure Active Directory 中，可以通过要求进行多重身份验证 (MFA) 来实现更强的帐户验证。

**要求对管理员的 mfa (预览)**  是一项[基准策略](concept-baseline-protection.md), 需要在每次登录时使用 mfa:

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员
* 支持人员管理员/密码管理员
* 帐务管理员
* 用户管理员

启用 "要求对管理员使用 MFA" 策略后, 将需要使用验证器应用为 MFA 注册以上九个管理员角色。 完成 MFA 注册后, 管理员将需要在每次登录时执行 MFA。

## <a name="deployment-considerations"></a>部署注意事项

因为 "**需要对管理员 (预览版) 的 MFA** " 策略适用于所有关键管理员, 所以为了确保顺利部署, 需要考虑几个注意事项。 这些注意事项包括: 在 Azure AD 中标识用户和服务原则, 这些用户和服务原则不能或不应执行 MFA, 以及不支持新式身份验证的组织使用的应用程序和客户端。

### <a name="legacy-protocols"></a>旧协议

电子邮件客户端使用旧的身份验证协议进行身份验证请求。 这些协议不支持 MFA。 Microsoft 所见到的大多数帐户损害都是由不良的执行组件对尝试绕过 MFA 的传统协议执行攻击导致的。 若要确保在登录到管理帐户时需要 MFA, 但不良执行组件无法绕过 MFA, 此策略将阻止从旧协议对管理员帐户发出的所有身份验证请求。

> [!WARNING]
> 启用此策略之前, 请确保你的管理员没有使用旧版身份验证协议。 请参阅文章[如何:有关详细信息, 请阻止使用条件](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)性访问 Azure AD 的旧式身份验证。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基线策略:需要对管理员 (预览版)** 进行预配置, 并且当你导航到 Azure 门户中的 "条件性访问" 边栏选项卡时, 它将显示在顶部。

若要启用此策略并保护管理员:

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中, 选择 **"基准策略":要求对管理员 (预览版)** 进行 MFA。
1. 将 "**启用策略**" 设置为 "**立即使用策略**"。
1. 单击 " **保存**"。

> [!WARNING]
> 将来如果此策略处于预览阶段,**会自动启用策略**。 我们删除了此选项, 以最大程度地减少对用户的影响。 如果在可用时选择了此选项, 则**不**会自动使用 "策略"。 如果他们想要使用此基准策略, 请参阅以上步骤以启用它。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
