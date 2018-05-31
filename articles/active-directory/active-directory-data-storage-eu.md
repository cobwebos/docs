---
title: Azure AD 在哪个位置存储欧洲客户的标识数据 | Microsoft Docs
description: 了解 Microsoft Azure Active Directory 在哪个位置存储其欧洲客户的标识相关数据。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 19dc163dbb6dd296a417f5c313a36c7f7c9e50d7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305571"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Microsoft Azure Active Directory (Azure AD) 在哪个位置存储欧洲客户的标识数据
Azure AD 可帮助管理用户标识，并创建智能化的访问策略用于帮助保护组织的资源。 标识数据的存储位置基于订阅服务时（例如，订阅 Office 365 或 Azure 时） 组织提供的地址。 有关标识数据存储位置的具体信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located)部分。

尽管大部分 Azure AD 相关的欧洲标识数据保留在欧洲的数据中心，但有五个用户相关的属性通常会存储在美国的数据中心。 这些属性是 GivenName、Surname、userPrincipalName、Domain 和 PasswordHash。 PasswordHash 有一种例外情况：如果某人使用的本地联合身份验证方法会阻止 PasswordHash 值与 Azure AD 同步，则该属性不会存储在美国。 此外，还有一些运营性的特定于服务的数据需要经过正常的 Azure AD 操作，它们会存储在美国，但这些数据不包括任何个人数据。

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>存储在欧洲数据中心外部的欧洲客户数据

营业地址位于欧洲的组织的大部分 Azure AD 相关欧洲标识数据都会保留在欧洲数据中心。 不会存储在欧洲数据中心的 Azure AD 数据包括：

- **标识相关的属性**

    以下标识相关的属性将复制到美国：

    - GivenName
    - Surname
    - userPrincipalName
    - 域
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure 多重身份验证 (MFA) 和 Azure AD 自助密码重置 (SSPR)**
    
    MFA 在欧洲数据中心存储所有用户静态数据。 但是，某些特定于 MFA 服务的数据存储在美国，包括：
    
    - 如果使用 MFA 或 SSPR，双重身份验证及其相关个人数据可能会存储在美国。
        - 使用电话呼叫或短信的所有双重身份验证可能由美国的运营商完成。
        - 使用 Microsoft Authenticator 应用的推送通知需要从制造商的通知服务（Apple 或 Google）发送的通知，此过程可能在欧洲外部完成。
        - OATH 代码始终在美国验证。 
    - 某些 MFA 和 SSPR 日志在美国存储 30 天，不管身份验证类型是什么。

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Azure AD B2C 在欧洲数据中心存储所有用户静态数据。 但是，运行日志（已删除个人数据）会保留在用户访问服务时所在的位置。 例如，如果某个 B2C 用户访问位于美国的服务，则运行日志会保留在美国。此外，不包含个人数据的所有策略配置数据只会存储在美国。有关策略配置的详细信息，请参阅 [Azure Active Directory B2C：内置策略](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies)一文。

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Azure AD B2B 在欧洲数据中心存储所有用户静态数据。 但是，B2B 将表格中的非个人元数据存储在美国数据中心。 此表格包含 redeemUrl、invitationTicket、资源租户 ID、InviteRedirectUrl 和 InviterAppId 等字段。

- **Microsoft Azure Active Directory 域服务 (Azure AD DS)**

    Azure AD DS 将用户数据存储在客户选择的 Azure 虚拟网络所在的同一位置。 因此，如果该网络位于欧洲外部，则会复制数据并将其存储在欧洲外部。

- **与 Azure AD 集成的服务和应用**

    与 Azure AD 集成的任何服务和应用都有权访问标识数据。 请评估每个服务和应用，以确定该特定服务和应用如何处理标识数据，以及它们是否满足公司的数据存储要求。

    有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located)部分。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章。
- [Azure Active Directory 入门](get-started-azure-ad.md)
- [什么是多重身份验证？](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD 自助密码重置](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [什么是 Azure Active Directory B2C？](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [什么是 Azure AD B2B 协作？](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) 域服务](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
