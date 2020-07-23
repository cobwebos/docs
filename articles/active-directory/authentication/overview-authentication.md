---
title: Azure Active Directory 身份验证概述
description: 了解通过 Azure Active Directory 进行用户登录的不同身份验证方法和安全功能。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2a6b5c22e2988ed33e6dc15a4729e7bfe938c5f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786911"
---
# <a name="what-is-azure-active-directory-authentication"></a>Azure Active Directory 身份验证是什么？

标识平台的主要功能之一就是在用户登录到设备、应用程序或服务时，对凭据进行验证或身份验证  。 在 Azure Active Directory (Azure AD) 中，身份验证不仅仅涉及对用户名和密码的验证。 Azure AD 身份验证包括以下组件，用于提高安全性并降低对技术人员帮助的需求：

* 自助式密码重置
* Azure 多重身份验证
* 用于将密码更改写回到本地环境的混合集成
* 用于对本地环境强制实施密码保护策略的混合集成
* 无密码身份验证

## <a name="improve-the-end-user-experience"></a>改善最终用户体验

Azure AD 有助于保护用户的身份并简化其登录体验。 自助式密码重置等功能允许用户从任何设备使用 Web 浏览器更新或更改密码。 如果用户忘记了密码或帐户被锁定，此功能尤其有用。 在不等待支持人员或管理员提供支持的情况下，用户可以取消对自己的阻止，继续工作。

Azure 多重身份验证可以让用户在登录期间选择其他形式的身份验证，如电话呼叫或移动应用通知。 此功能减少了单一、固定形式的辅助身份验证（如硬件令牌）的要求。 如果用户当前没有一种形式的附加身份验证，则可选择其他方法，继续工作。

![登录屏幕上使用的身份验证方法](media/concept-authentication-methods/overview-login.png)

无密码身份验证根本不需要用户创建并记住安全密码。 Windows Hello 企业版或 FIDO2 安全密钥等功能让用户无需密码即可登录到设备或应用程序。 此功能可以降低跨不同环境管理密码的复杂性。

## <a name="self-service-password-reset"></a>自助式密码重置

自助式密码重置使用户能够更改或重置其密码，而不需要管理员或支持人员。 如果用户的帐户被锁定或用户忘记了自己的密码，他们可以按照提示取消对自己的阻止，回到工作状态。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。

自助式密码重置适用于以下情况：

* **密码更改 -** 用户知道自己的密码，但想要将其更改为新的。
* **密码重置 -** 用户无法登录（例如，忘记了密码），但想要重置其密码。
* **帐户解锁 -** 用户因其帐户被锁定而无法登录，但想要解锁其帐户。

当用户使用自助式密码重置更新或重置其密码时，也可将该密码写回到本地 Active Directory 环境。 密码写回可确保用户能够立即在本地设备和应用程序中使用更新的凭据。

## <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

多重身份验证是一种过程。在该过程中，系统会在用户登录时提示其输入其他形式的标识，例如在其手机上输入代码或提供指纹扫描。

如果只使用密码对用户进行身份验证，则会留下不安全的矢量，容易受到攻击。 如果密码弱或者已在其他位置公开，那么如何确定是该用户在使用用户名和密码登录，还是攻击者在登录？ 需要另一种形式的身份验证时，会提高安全性，因为攻击者并不容易获取或复制进行多重身份验证所需的额外内容。

![不同形式的多重身份验证的概念图](./media/concept-mfa-howitworks/methods.png)

Azure 多重身份验证需要以下身份验证方法中的两种或更多种才能运作：

* 你知道的某样东西，通常为密码。
* 你有的某样东西，例如无法轻易复制的可信设备，如电话或硬件密钥。
* 自身的特征 - 生物识别，如指纹或面部扫描。

用户只需执行一个步骤即可自行注册自助式密码重置和 Azure 多重身份验证，这样可以简化加入体验。 管理员可以定义能够使用的辅助身份验证形式。 当用户执行自助式密码重置以进一步保护该过程时，也可能需要 Azure 多重身份验证。

## <a name="password-protection"></a>密码保护

默认情况下，Azure AD 会阻止弱密码，如 *Password1*。 全局禁止的密码列表会自动更新并强制实施，其中包含已知弱密码。 如果 Azure AD 用户尝试将其密码设置为这些弱密码之一，则会收到要求他们选择更安全密码的通知。

若要提高安全性，可以定义自定义密码保护策略。 这些策略可以使用筛选器来阻止包含名称（例如 *Contoso*）或位置（例如*伦敦*）的密码的任何变体。

为确保混合安全性，可以将 Azure AD 密码保护与本地 Active Directory 环境集成。 在本地环境中安装的组件会接收 Azure AD 的全局禁止密码列表和自定义密码保护策略，而域控制器则会使用它们来处理密码更改事件。 这种混合方法可确保无论用户如何更改其凭据或在什么位置更改其凭据，都可以强制使用强密码。

## <a name="passwordless-authentication"></a>无密码身份验证

许多环境的最终目标是在登录事件中杜绝密码的使用。 Azure 密码保护或 Azure 多重身份验证之类的功能有助于提高安全性，但用户名和密码仍然是一种弱的身份验证形式，可能会泄露或受到强力攻击。

![导致无密码的身份验证过程的安全性和便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

使用无密码方法登录时，会通过使用 Windows Hello 企业版的生物识别或 FIDO2 安全密钥等方法来提供凭据。 攻击者无法轻松地复制这些身份验证方法。

可以通过 Azure AD 使用无密码方法进行本机身份验证，简化用户的登录体验并降低受到攻击的风险。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅[自助式密码重置 (SSPR) 的教程][tutorial-sspr]和 [Azure 多重身份验证][tutorial-azure-mfa]。

若要详细了解自助式密码重置概念，请参阅 [Azure AD 自助式密码重置的工作原理][concept-sspr]。

若要详细了解多重身份验证概念，请参阅 [Azure 多重身份验证的工作原理][concept-mfa]。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
