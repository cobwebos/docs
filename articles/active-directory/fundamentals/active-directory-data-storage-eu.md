---
title: 欧洲客户标识数据存储 - Azure Active Directory | Microsoft Docs
description: 了解 MAzure Active Directory 在哪个位置存储其欧洲客户的标识相关数据。
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82a78953c4385f7688705d4ab3f697be9c3ddbd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235168"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中的欧洲客户标识数据存储
由 Azure AD 中标识数据存储在基于 Office 365 和 Azure 等 Microsoft Online 服务订阅时，你的组织提供的地址的地理位置时。 有关标识数据的存储位置的信息，可以使用[位于数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft 信任中心部分。

对于提供的地址在欧洲客户，Azure AD 会保留大多数欧洲数据中心内的标识数据。 本文档提供有关由 Azure AD 服务存储在欧洲之外的任何数据的信息。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多重身份验证 (MFA)
    
- 所有的双因素身份验证使用电话呼叫或短信来源于美国数据中心，并且也由全球提供商路由。
- 推送通知使用 Microsoft Authenticator 应用源自美国数据中心。 此外，设备供应商特定的服务也可能会进入 play 和欧洲之外可能是这些服务。
- OATH 代码始终在美国验证。

有关哪些用户信息收集的 Azure 多重身份验证服务器 （MFA 服务器） 和基于云的 Azure MFA 的详细信息，请参阅[Azure 多重身份验证用户数据收集](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 策略配置数据和密钥容器存储在美国数据中心。 这些不包含任何用户个人数据。 有关策略配置的详细信息，请参阅 [Azure Active Directory B2C：内置策略](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 存储邀请与兑换链接，并将重定向 URL 在美国数据中心中的信息。 此外，取消订阅接收 B2B 邀请的用户的电子邮件地址也存储在美国数据中心中。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 域服务 (Azure AD DS)

Azure AD DS 将用户数据存储在客户选择的 Azure 虚拟网络所在的同一位置。 因此，如果该网络位于欧洲外部，则会复制数据并将其存储在欧洲外部。

## <a name="other-considerations"></a>其他注意事项

服务和与 Azure AD 集成的应用程序有权访问标识数据。 评估每个服务和应用程序，用于确定如何标识数据处理由该特定服务和应用程序，以及它们是否满足你公司的数据存储要求。

有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 自助密码重置](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [什么是 Azure Active Directory B2C？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [什么是 Azure AD B2B 协作？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
