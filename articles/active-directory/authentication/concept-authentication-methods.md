---
title: 身份验证方法和功能 - Azure Active Directory
description: 了解 Azure Active Directory 中可用的不同身份验证方法和功能，以帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c331334df64ea1bea45949152a34241a73fa5bb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666154"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory 中有哪些可用的身份验证和验证方法？

作为 Azure Active Directory (Azure AD) 中帐户的登录体验的一部分，用户可以通过不同的方式进行身份验证。 用户名和密码是用户历来提供凭据的最常见方式。 通过 Azure AD 中的新式身份验证和安全功能，应通过更安全的身份验证方法来补充或替换基本密码。

![Azure AD 中的优势和首选身份验证方法表](media/concept-authentication-methods/authentication-methods.png)

无密码身份验证方法（例如 Windows Hello、FIDO2 安全密钥和 Microsoft Authenticator 应用）提供最安全的登录事件。

当用户登录时，Azure 多重身份验证添加了更多的安全性，而不是使用密码。 系统可能会提示用户提供其他形式的身份验证，例如响应推送通知、输入软件或硬件令牌中的代码或响应短信或电话呼叫。

为了简化用户的登录体验并注册 MFA 和 SSPR，我们建议你 [启用组合的安全信息注册](howto-registration-mfa-sspr-combined.md)。 对于复原功能，建议您要求用户注册多个身份验证方法。 如果在登录或 SSPR 期间一个方法不可用于用户，则他们可以选择使用其他方法进行身份验证。 有关详细信息，请参阅 [在 Azure AD 中创建弹性访问控制管理策略](concept-resilient-controls.md)。

## <a name="authentication-method-strength-and-security"></a>身份验证方法强度和安全性

在组织中部署 Azure 多重身份验证等功能时，请查看可用的身份验证方法。 根据安全性、可用性和可用性，选择满足或超出要求的方法。 如果可能，请使用身份验证方法和最高安全级别。

下表概述了可用的身份验证方法的安全注意事项。 可用性是指用户可以使用身份验证方法，而不是 Azure AD 中服务可用性的指示：

| 身份验证方法          | 安全 | 可用性 | 可用性 |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | 高     | 高      | 高         |
| Microsoft Authenticator 应用    | 高     | 高      | 高         |
|  (预览版的 FIDO2 安全密钥)    | 高     | 高      | 高         |
| OATH 硬件令牌（预览版） | 中   | 中    | 高         |
| OATH 软件令牌           | 中   | 中    | 高         |
| SMS                            | 中型   | 高      | 中等       |
| 语音                          | 中   | 中    | 中       |
| Password                       | 低      | 高      | 高         |

有关安全性的详细信息，请参阅 [身份验证漏洞和攻击媒介](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)。

> [!TIP]
> 为实现灵活性和可用性，我们建议使用 Microsoft Authenticator 应用。 此身份验证方法提供最佳用户体验和多种模式，如无密码、MFA 推送通知和 OATH 代码。

## <a name="how-each-authentication-method-works"></a>每种身份验证方法的工作方式

登录到应用程序或设备时，某些身份验证方法可用作主要因素，如使用 FIDO2 安全密钥或密码。 使用 Azure 多重身份验证或 SSPR 时，其他身份验证方法仅作为辅助因素提供。

下表概述了在登录事件期间可以使用身份验证方法的时间：

| 方法                         | 主要身份验证 | 辅助身份验证  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | 是                    | MFA                       |
| Microsoft Authenticator 应用    | 是（预览版）          | MFA 和 SSPR              |
|  (预览版的 FIDO2 安全密钥)    | 是                    | MFA                       |
| OATH 硬件令牌（预览版） | 否                     | MFA                       |
| OATH 软件令牌           | 否                     | MFA                       |
| SMS                            | 是（预览版）          | MFA 和 SSPR              |
| 语音呼叫                     | 否                     | MFA 和 SSPR              |
| 密码                       | 是                    |                           |

所有这些身份验证方法都可以在 Azure 门户中进行配置，并且在更多情况下都使用 [Microsoft Graph REST API beta 版](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

若要详细了解每种身份验证方法的工作方式，请参阅以下单独的概念文章：

* [Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator 应用](concept-authentication-authenticator-app.md)
* [ (预览版的 FIDO2 安全密钥) ](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH 硬件令牌（预览版）](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH 软件令牌](concept-authentication-oath-tokens.md#oath-software-tokens)
* SMS [登录 (预览) ](howto-authentication-sms-signin.md) 和 [验证](concept-authentication-phone-options.md#mobile-phone-verification)
* [语音呼叫验证](concept-authentication-phone-options.md)
* Password

> [!NOTE]
> 在 Azure AD 中，密码通常是主要身份验证方法之一。 不能禁用密码身份验证方法。 如果使用密码作为主要身份验证因素，请使用 Azure 多重身份验证提高登录事件的安全性。

在某些情况下，可以使用以下附加验证方法：

* [应用密码](howto-mfa-app-passwords.md) -用于不支持新式身份验证并且可配置为按用户 Azure 多重身份验证的旧应用程序。
* [安全问题](concept-authentication-security-questions.md) -仅用于 SSPR
* [电子邮件地址](concept-sspr-howitworks.md#authentication-methods) -仅用于 SSPR

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure 多重身份验证][tutorial-azure-mfa]。

要详细了解 SSPR 概念，请参阅[ Azure AD 自助式密码重置的工作原理][concept-sspr]。

要详细了解 MFA 概念，请参阅[ Azure 多重身份验证的工作原理][concept-mfa]。

详细了解如何使用 [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)配置身份验证方法。

若要查看正在使用的身份验证方法，请参阅 [使用 PowerShell 进行 Azure 多重身份验证身份验证方法分析](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
