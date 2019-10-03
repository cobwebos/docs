---
title: 欧洲客户标识数据存储 - Azure Active Directory | Microsoft Docs
description: 了解 MAzure Active Directory 在哪个位置存储其欧洲客户的标识相关数据。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178f81cf42e5c57be4a0b69ada6560d46951a3a3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942844"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中的欧洲客户标识数据存储
标识数据由 Azure AD 存储在地理位置, 在订阅 Microsoft Online services (如 Office 365 和 Azure) 时, 该地址基于组织提供的地址。 若要了解标识数据的存储位置, 可以使用 Microsoft 信任中心的 "[你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) " 部分。

对于在欧洲提供地址的客户, Azure AD 保留欧洲数据中心内的大多数标识数据。 本文档提供了有关 Azure AD 服务在欧洲之外存储的任何数据的信息。

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多重身份验证 (MFA)
    
- 使用电话呼叫或短信的所有双因素身份验证都源自我们的数据中心, 并且还由全局提供商路由。
- 使用 Microsoft Authenticator 应用推送通知来自美国数据中心。 此外, 特定于设备供应商的服务也可能会起作用, 这些服务可能在欧洲境外。
- OATH 代码始终在美国验证。 

有关 Azure 多重身份验证服务器 (MFA 服务器) 和基于云的 Azure MFA 收集的用户信息的详细信息, 请参阅[Azure 多重身份验证用户数据收集](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection)。

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 策略配置数据和密钥容器存储在美国数据中心。 它们不包含任何用户个人数据。 有关策略配置的详细信息，请参阅 [Azure Active Directory B2C：内置策略](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 用兑换链接来存储邀请, 并在美国数据中心重定向 URL 信息。 此外, 取消订阅接收 B2B 邀请的用户的电子邮件地址也存储在美国数据中心。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 域服务 (Azure AD DS)

Azure AD DS 将用户数据存储在客户选择的 Azure 虚拟网络所在的同一位置。 因此，如果该网络位于欧洲外部，则会复制数据并将其存储在欧洲外部。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 中的联合身份验证
    
- 应用程序标识符 (AppID)-Azure Active Directory authentication 系统生成的唯一编号, 用于标识 Exchange 组织。
- 应用程序的已批准联合域列表
- 应用程序的令牌签名公钥 

有关 Microsoft Exchange server 中的联合的详细信息, 请[参阅联合:Exchange 2013 帮助](https://docs.microsoft.com/exchange/federation-exchange-2013-help)文章。


## <a name="other-considerations"></a>其他注意事项

与 Azure AD 集成的服务和应用程序可以访问标识数据。 评估每个服务和应用程序, 以确定该特定服务和应用程序如何处理标识数据, 以及这些服务和应用程序是否符合公司的数据存储要求。

有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Azure AD 自助密码重置](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [什么是 Azure Active Directory B2C？](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [什么是 Azure AD B2B 协作？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
