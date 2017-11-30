---
title: "Azure AD 自助服务密码重置概述 | Microsoft Docs"
description: "Azure AD 自助服务密码重置对组织有什么用处？"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a0ad153838b16604f2872cc3b56562e5762a3033
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2017
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>面向 IT 专业人员的 Azure AD 自助服务密码重置

使用 Azure Active Directory (Azure AD) 自助密码重置 (SSPR) 功能，用户可以根据需要随时随地自行重置密码。 同时，管理员可以控制重置用户密码的方式。 用户不再需要致电支持人员，只需重置其密码即可。 Azure AD SSPR 包括：

* **自助密码更改**：用户知道自己的密码，但想要将其更改为新内容。
* **自助密码重置**：用户无法登录，想要使用以下一个或多个已验证的身份验证方法重置其密码：
   * 向已验证的手机发送短信。
   * 向已验证的手机或办公电话发起电话呼叫。
   * 向已验证的辅助电子邮件帐户发送电子邮件。
   * 回答其安全问题。
* **自助帐户解锁**：用户无法使用其密码登录，并且已锁定。用户想要使用其身份验证方法解锁其帐户而无需管理员干预。

## <a name="why-choose-azure-ad-sspr"></a>为什么选择 Azure AD SSPR

Azure AD SSPR 有助于：

* **降低成本**，因为由支持人员协助的密码重置通常占 IT 组织的支持呼叫的 20%。 
* 通过向最终用户提供解决其自己的密码问题的能力**改善最终用户体验**并**减少支持人员数量**。 无需致电支持人员或建立支持请求。
* **促进移动**，因为用户可以从任何位置重置其密码。
* **保持控制**安全策略。 管理员可继续强制实施当前已有的策略。

如果已准备好，可以按照我们的[快速入门指南](active-directory-passwords-getting-started.md)开始使用 Azure AD SSPR。 可以快速为用户提供重置其自己的密码的功能。

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR 可用性

Azure AD SSPR 在三个层中可用，具体取决于订阅：

* **Azure AD Free**：仅限云的管理员可以重置其自己的密码。
* **Azure AD Basic** 或任何**付费 Office 365 订阅**：仅限云的用户可以重置其自己的密码。
* **Azure AD Premium**：任何用户或管理员（包括仅限云、联合或密码同步的用户）都可以重置其自己的密码。 本地密码要求启用密码写回。

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 定价、SLA、更新和路线图

可在以下站点中找到有关许可、定价和未来计划的详细信息：

* [Azure AD 定价详细信息](https://azure.microsoft.com/pricing/details/active-directory/)
* [Office 365 定价](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Azure 服务级别协议](https://azure.microsoft.com/support/legal/sla/)
* [Microsoft Online Services 的服务级别协议](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure 更新](https://azure.microsoft.com/updates/)
* [Azure 路线图](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>后续步骤

* 是否已准备好开始使用 SSPR？ [设置 Azure AD 自助密码重置](active-directory-passwords-getting-started.md)。
* 使用[推出指南](active-directory-passwords-best-practices.md)中的指南计划到用户的成功 SSPR 部署。
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)。
* [注册自助服务密码重置](active-directory-passwords-reset-register.md)。
