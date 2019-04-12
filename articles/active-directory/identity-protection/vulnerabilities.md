---
title: Azure Active Directory Identity Protection 检测到的漏洞
description: Azure Active Directory Identity Protection 检测到的漏洞概述。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80faf1899da0f16d25b9b7c68cb0edcbc1276632
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488227"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 检测到的漏洞

漏洞是可能由攻击者利用的环境中的弱点。 我们建议管理员解决这些漏洞，以提高其组织的安全状况。

![Identity Protection 报告漏洞](./media/vulnerabilities/identity-protection-vulnerabilities.png)

以下部分概述了 Identity Protection 报告的漏洞。

## <a name="multi-factor-authentication-registration-not-configured"></a>未配置多重身份验证注册

此漏洞可帮助评估组织中的部署 Azure 多重身份验证。

若要查看计数未注册 mfa，用户单击此漏洞，您将重定向到标识安全功能分数中的统计信息。

Azure 多重身份验证提供了第二层到用户身份验证的安全性。 它可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 Azure 多重身份验证提供了易于使用的验证选项，如：

* 电话呼叫
* 短信
* 移动应用通知
* OTP 验证码

我们建议将 Azure 多重身份验证用于用户登录。多重身份验证在通过 Identity Protection 提供的基于风险的条件访问策略中起重要作用。

有关详细信息，请参阅[什么是 Azure 多重身份验证？](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>非托管的云应用

此漏洞可帮助你标识组织中的非托管云应用。

IT 人员通常不知道在其组织中的所有云应用程序。 很容易理解为什么管理员对未经授权访问公司数据、可能的数据泄漏和其他安全风险有所顾虑。

我们建议部署 Cloud Discovery 发现非托管的云应用程序，以及管理这些应用程序使用 Azure Active Directory。

有关详细信息，请参阅 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)。

## <a name="security-alerts-from-privileged-identity-management"></a>来自 Privileged Identity Management 的安全警报

此漏洞可帮助你发现和解决有关组织中特权标识的警报。  

为了允许用户执行特权操作，组织需要向用户授予 Azure AD、Azure 或 Office 365 资源或者其他 SaaS 应用的临时或永久访问特权。 其中每个特权用户都会增加组织的攻击面。 此漏洞可帮助你标识具有不必要访问特权的用户，并采取相应操作降低或消除他们带来的风险。

我们建议组织使用 Azure AD Privileged Identity Management 管理、 控制和监视特权的标识 Azure AD 中，以及 Office 365 或 Microsoft Intune 等其他 Microsoft online services。

有关详细信息，请参阅 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)。

## <a name="see-also"></a>另请参阅

[Azure Active Directory 标识保护](../active-directory-identityprotection.md)
