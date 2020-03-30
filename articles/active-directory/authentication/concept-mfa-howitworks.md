---
title: 其工作原理 Azure MFA - Azure 活动目录
description: Azure 多重身份验证可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484054"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>工作原理：Azure 多重身份验证

双重验证的安全性在于它的分层方法。 破坏多因素身份验证系统对于攻击者来说是巨大的挑战。 即使攻击者设法得到用户的密码，如果没有同时占有其他身份验证方法也没有用处。 它需要以下身份验证方法中的两种或多种才能运作：

* 你知道的某样东西（通常为密码）
* 具有的某样东西（无法轻易复制的可信设备，如电话）
* 自身的特征（生物辨识系统）

<center>

![概念身份验证方法图像](./media/concept-mfa-howitworks/methods.png)</center>

Azure 多重身份验证 (MFA) 有助于保护对数据和应用程序的访问，同时满足用户对简单性的需求。 它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的[身份验证方法](concept-authentication-methods.md)提供增强式身份验证。 根据管理员制定的配置决策，用户可能会受到 MFA 的质疑，也可能不会受到 MFA 的质疑。

## <a name="how-to-get-multi-factor-authentication"></a>如何获取多重身份验证？

多重身份验证属于以下产品/服务的一部分：

* **Azure 活动目录高级**版或**Microsoft 365 业务**版 - 使用条件访问策略使用 Azure 多重身份验证的完全功能使用，以需要多重身份验证。

* **Azure AD 免费**或独立**Office 365**许可证 - 使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)需要为用户和管理员进行多重身份验证。

* **Azure Active Directory 全局管理员** - 提供 Azure 多重身份验证的部分功能，可用来保护全局管理员帐户。

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客户无法再将 Azure 多重身份验证作为独立产品进行购买。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

## <a name="supportability"></a>可支持性

由于大多数用户习惯只使用密码进行身份验证，因此，贵组织必须让所有用户了解此过程。 如果用户熟悉该过程，他们就不会在遇到与 MFA 相关的小问题时经常呼叫支持人员。 但是，在某些情况下，需要暂时禁用 MFA。 使用以下指导原则了解如何处理这种情况：

* 请对支持人员进行培训，以处理用户因无法访问其身份验证方法或操作不正确而无法登录的情况。
   * 使用 Azure MFA 服务的条件访问策略，支持人员可以将用户添加到从需要 MFA 的策略中排除的组。
* 请考虑使用条件访问命名位置作为最小化两步验证提示的方法。 借助此功能，管理员可以绕过从安全受信任的网络位置登录的用户（如用于新用户加入的网段）的两步验证。
* 部署[Azure AD 标识保护](../active-directory-identityprotection.md)，并根据风险检测触发两步验证。

## <a name="next-steps"></a>后续步骤

- [分步 Azure 多重身份验证部署](howto-mfa-getstarted.md)
