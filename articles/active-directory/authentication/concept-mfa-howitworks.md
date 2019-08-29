---
title: Azure 多重身份验证-它的工作原理-Azure Active Directory
description: Azure 多重身份验证可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125132"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>工作原理：Azure 多重身份验证

双重验证的安全性在于它的分层方法。 破坏多因素身份验证系统对于攻击者来说是巨大的挑战。 即使攻击者设法得到用户的密码，如果没有同时占有其他身份验证方法也没有用处。 它需要以下身份验证方法中的两种或多种才能运作：

* 您知道的信息（通常为密码）
* 您拥有的东西（不易被复制的受信任设备，如电话）
* 您的特征（生物识别）

<center>

![概念性身份验证方法图像](./media/concept-mfa-howitworks/methods.png)</center>

Azure 多重身份验证 (MFA) 有助于保护对数据和应用程序的访问，同时满足用户对简单性的需求。 它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的[身份验证方法](concept-authentication-methods.md)提供增强式身份验证。 根据管理员制定的配置决策，用户可能会受到 MFA 的质疑，也可能不会受到 MFA 的质疑。

## <a name="how-to-get-multi-factor-authentication"></a>如何获取多重身份验证？

多重身份验证属于以下产品/服务的一部分：

* **Azure Active Directory Premium**或**Microsoft 365 商业版**-使用条件性访问策略来要求执行多重身份验证的 Azure 多重身份验证的完整功能。

* **Azure AD Free**或独立**Office 365**许可证-使用预先创建的[条件访问基线保护策略](../conditional-access/concept-baseline-protection.md), 要求用户和管理员进行多重身份验证。

* **Azure Active Directory 全局管理员** - 提供 Azure 多重身份验证的部分功能，可用来保护全局管理员帐户。

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客户无法再将 Azure 多重身份验证作为独立产品进行购买。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

## <a name="supportability"></a>可支持性

由于大多数用户习惯只使用密码进行身份验证，因此，贵组织必须让所有用户了解此过程。 如果用户熟悉该过程，他们就不会在遇到与 MFA 相关的小问题时经常呼叫支持人员。 但是，在某些情况下，需要暂时禁用 MFA。 使用以下指导原则了解如何处理这种情况：

* 请对支持人员进行培训，以处理用户因无法访问其身份验证方法或操作不正确而无法登录的情况。
   * 使用 Azure MFA 服务的条件访问策略, 你的支持人员可以将用户添加到从需要 MFA 的策略中排除的组。
* 请考虑使用条件性访问命名位置作为最小化双重验证提示的方式。 使用此功能, 管理员可以绕过从安全的受信任的网络位置 (例如用于新用户载入的网络分段) 登录的用户的双重验证。
* 部署[Azure AD Identity Protection](../active-directory-identityprotection.md)并根据风险检测来触发双重验证。

## <a name="next-steps"></a>后续步骤

- [循序渐进 Azure 多重身份验证部署](howto-mfa-getstarted.md)
