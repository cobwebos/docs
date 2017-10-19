---
title: "Azure Active Directory Identity Protection 检测到的漏洞 | Microsoft 文档"
description: "Azure Active Directory Identity Protection 检测到的漏洞概述。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 364873ff54099a6123e40b12e819d1745751f285
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 检测到的漏洞
漏洞是环境中可能由攻击者利用的弱点。 我们建议解决这些漏洞，以提高组织的安全状态并防止攻击者利用它们。


![漏洞](./media/active-directory-identityprotection-vulnerabilities/101.png "漏洞")



以下部分概述了 Identity Protection 报告的漏洞。

## <a name="multi-factor-authentication-registration-not-configured"></a>多重身份验证注册未配置
此漏洞可帮助你控制在组织中部署 Azure 多重身份验证的操作。 

Azure 多重身份验证提供用户身份验证的第二层安全。 它可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种简单的验证选项（例如电话、短信、移动应用通知或验证码）和第三方 OATH 令牌）来提供强大的身份验证。

我们建议将 Azure 多重身份验证用于用户登录。多重身份验证在通过 Identity Protection 提供的基于风险的条件访问策略中起重要作用。

有关更多详细信息，请参阅[什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>非托管的云应用
此漏洞可帮助你标识组织中的非托管云应用。

在现代企业中，IT 部门不一定了解组织用户执行其工作所使用的所有云应用程序。 很容易知道为什么管理员对未经授权访问公司数据、可能的数据泄漏和其他安全风险有所顾虑。 

我们建议组织部署 Cloud App Discovery，发现非托管的云应用程序并使用 Azure Active Directory 管理这些应用程序。

有关更多详细信息，请参阅[使用 Cloud App Discovery 查找非托管的云应用程序](active-directory-cloudappdiscovery-whatis.md)。

## <a name="security-alerts-from-privileged-identity-management"></a>来自 Privileged Identity Management 的安全警报
此漏洞可帮助你发现和解决有关组织中特权标识的警报。  

为了允许用户执行特权操作，组织需要向用户授予 Azure AD、Azure 或 Office 365 资源或者其他 SaaS 应用的临时或永久访问特权。 其中每个特权用户都会增加组织的攻击面。 此漏洞可帮助你标识具有不必要访问特权的用户，并采取相应操作降低或消除他们带来的风险。 

我们建议组织使用 Azure AD Privileged Identity Management 管理、控制和监视特权标识及其对 Azure AD 中和 Office 365 或 Microsoft Intune 等其他 Microsoft Online Services 中资源的访问权限。

有关更多详细信息，请参阅 [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。 

## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


