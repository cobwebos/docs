---
title: 基准策略适用于管理员的 Azure Active Directory 要求使用 MFA
description: 条件性访问策略以要求管理员的多重身份验证
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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112431"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>基准策略：要求管理员进行 MFA

对特权帐户具有访问权限的用户对你的环境具有不受限制的访问权限。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 增强对特权帐户的保护的一种常用方法是要求在使用这些帐户登录时进行更强的帐户验证。 在 Azure Active Directory 中，可以通过要求进行多重身份验证 (MFA) 来实现更强的帐户验证。

**适用于管理员要求使用 MFA** 是[基准策略](concept-baseline-protection.md)每当以下特权的管理员角色之一登录时需要 MFA:

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员
* 支持管理员 / 密码管理员
* 计费管理员
* 用户管理员

启用管理员策略需要 MFA，时更高版本的九个管理员角色将需要使用身份验证器应用的 mfa 注册。 MFA 注册完成后，管理员将需要执行每次登录的 MFA。

![为管理员基线策略需要 MFA](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>部署注意事项

因为**适用于管理员要求使用 MFA**策略适用于所有关键的管理员，几个注意事项需要进行，以确保顺利完成部署。 这些注意事项包括识别用户并不能或不应执行 MFA，以及应用程序和由你的组织不支持新式身份验证的客户端的 Azure AD 中的服务主体。

### <a name="legacy-protocols"></a>旧协议

邮件客户端使用旧式身份验证协议 （IMAP、 SMTP、 POP3、 等） 进行身份验证请求。 这些协议不支持 MFA。 大部分看到由 Microsoft 帐户破坏而引起不良参与方执行攻击试图绕过 MFA 的旧协议。 为了确保登录到管理帐户时需要 MFA 和不良参与方不能绕过 MFA，此策略将阻止旧版协议通过对管理员帐户所做的所有身份验证请求。

> [!WARNING]
> 启用此策略之前，请确保你的管理员不使用传统的身份验证协议。 请参阅文章[如何：阻止到 Azure AD 条件性访问与传统的身份验证](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)有关详细信息。

### <a name="user-exclusions"></a>用户排除项

此基线策略提供选项来排除用户。 在启用之前该策略为你的租户，我们建议排除以下帐户：

* **紧急访问**或**break glass**以防止租户范围内帐户锁定的帐户。 在所有管理员被都锁定在租户外部不太可能方案中，可以使用紧急访问管理帐户能够登录到租户采取步骤来恢复访问权限。
   * 可在本文中，找到更多信息[在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**并**服务原则**，例如 Azure AD Connect 同步帐户。 服务帐户是不受限于任何特定用户的非交互式帐户。 它们通常由后端服务，并且允许以编程方式访问应用程序。 应排除服务帐户，因为不能以编程方式完成 MFA。
   * 如果你的组织中的脚本或代码中使用这些帐户，请考虑将它们替换为 [托管标识](../managed-identities-azure-resources/overview.md)。 作为临时的解决方法，可以从基准策略中排除这些特定的帐户。
* 不具有或不能使用智能手机的用户。
   * 此策略要求管理员为使用 Microsoft Authenticator 应用的 MFA 注册。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基准策略：适用于管理员要求 MFA**预配置，此时将显示在顶部导航到在 Azure 门户中的条件性访问边栏选项卡时。

若要启用此策略并保护你的管理员：

1. 登录到 **Azure 门户** 作为全局管理员、 安全管理员或条件性访问管理员。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中，选择**基准策略：适用于管理员要求 MFA**。
1. 设置**启用策略**到**立即使用策略**。
1. 通过单击添加任何用户排除项**用户** > **选择排除的用户**，然后选择需要排除的用户。 单击**选择**然后**完成**。
1. 单击 **保存**。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/azure-ad-secure-steps.md)
* [什么是 Azure Active Directory 中条件性访问？](overview.md)
