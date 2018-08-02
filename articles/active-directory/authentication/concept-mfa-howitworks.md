---
title: Azure 多重身份验证 - 工作原理
description: Azure 多重身份验证可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 25af61ce51828691d08210f4089afa0cf56b9846
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257833"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>工作原理：Azure 多重身份验证

双重验证的安全性在于它的分层方法。 破坏多因素身份验证系统对于攻击者来说是巨大的挑战。 即使攻击者设法得到用户的密码，如果没有同时占有其他身份验证方法也没有用处。 它需要以下身份验证方法中的两种或多种才能运作：

* 你知道的某样东西（通常为密码）
* 具有的某样东西（无法轻易复制的可信设备，如电话）
* 自身的特征（生物辨识系统）

<center>![概念身份验证方法图像](./media/concept-mfa-howitworks/methods.png)</center>

Azure 多重身份验证 (MFA) 有助于保护对数据和应用程序的访问，同时满足用户对简单性的需求。 它通过要求第二种形式的身份验证提供额外的安全性，并通过一系列简单的[身份验证方法](concept-authentication-methods.md)提供增强式身份验证。

## <a name="how-to-get-multi-factor-authentication"></a>如何获取多重身份验证？

多重身份验证属于以下产品/服务的一部分：

* **Azure Active Directory Premium 许可证** - 使用完整版 Azure 多重身份验证服务（云）或 Azure 多重身份验证服务器（本地）。
   * **Azure MFA 服务（云）** - 此选项是用于新部署的推荐方式。 在云中，Azure MFA 无需本地基础结构，可供联合（或仅限云）用户使用。
   * **Azure MFA 服务器** - 如果贵组织想要管理相关的基础结构元素，并且已在本地环境中部署了 AD FS，则可以选择这种方式。
* **适用于 Office 365 的多重身份验证** - 订阅中包含 Azure 多重身份验证的部分功能。 有关适用于 Office 365 的 MFA 详细信息，请参阅 [Office 365 部署的多重身份验证计划](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba)。
* **Azure Active Directory 全局管理员** - 提供 Azure 多重身份验证的部分功能，可用来保护全局管理员帐户。

### <a name="auth-provider-or-mfa-license"></a>身份验证提供程序或 MFA 许可证

如果有 Azure AD Premium 或包含 Azure AD Premium 的许可证捆绑包，那么你已拥有 Azure MFA。 组织无需使用任何其他组件便可将双重验证功能扩展到所有用户。 只需将许可证分配给用户，然后便可以启用 MFA。

如果没有包含 Azure MFA 的许可证或没有足够的许可证来涵盖所有用户，则可以创建 [MFA 身份验证提供程序](concept-mfa-authprovider.md)，从而将 MFA 的全部功能扩展到需要它们的用户。 

> [!IMPORTANT]
> 如果没有足够的许可证供所有用户使用，则可以创建按用户的多重身份验证提供程序来涵盖组织的其余人员。 不要创建按身份验证的多重身份验证提供程序。 否则，最终可能要为已有许可证的用户的验证请求付费。

## <a name="supportability"></a>可支持性

由于大多数用户习惯只使用密码进行身份验证，因此，贵组织必须让所有用户了解此过程。 如果用户熟悉该过程，他们就不会在遇到与 MFA 相关的小问题时经常呼叫支持人员。 但是，在某些情况下，需要暂时禁用 MFA。 使用以下指导原则了解如何处理这种情况：

* 请对支持人员进行培训，以处理用户因无法访问其身份验证方法或操作不正确而无法登录的情况。
   * 使用 Azure MFA 服务的条件访问策略，支持人员可以将用户添加到从要求 MFA 的策略中排除的组中。
   * 支持人员可以为 Azure MFA 服务器用户启用临时的免验证一次，让用户在无需双重验证的情况下进行身份验证。 免验证是暂时性的，会在指定的秒数后过期。
   * 使用 Azure MFA 服务的条件访问策略，支持人员可以将用户添加到从要求 MFA 的策略中排除的组中。
* 请考虑使用受信任的 IP 或已命名的位置，以此减少双重验证提示。 借助此功能，托管或联合租户的管理员可对从受信任的网络位置（例如组织的 Intranet）登录的用户免除双重验证。
* 部署 [Azure AD Identity Protection](../active-directory-identityprotection.md) 并根据风险事件触发双重验证。

## <a name="next-steps"></a>后续步骤

- 获取分步 MFA [部署计划](https://aka.ms/MFADeploymentPlan)

- 查找有关[为用户授予许可](concept-mfa-licensing.md)的详细信息

- 获取有关[要部署的版本](concept-mfa-whichversion.md)详细信息

- 阅读[常见问题解答](multi-factor-authentication-faq.md)
