---
title: 通过 Azure AD 启用试用 | Azure
description: 在 Azure 市场和 AppSource 中使用 Azure Active Directory (Azure AD) 为应用和服务发布者启用“试用”列表类型
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/04/2018
ms.author: ellacroi
ms.openlocfilehash: 4140ba98c0c65c22674c61dc7266818af904e777
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825803"
---
# <a name="enable-trial-using-azure-ad"></a>通过 Azure AD 启用试用  
Azure Active Directory (Azure AD) 是一种云标识服务，它使用行业标准框架（OAuth 和 OpenID Connect）实现使用 Microsoft 工作或学校帐户进行身份验证。  
*   有关 Azure AD 的详细信息，请访问 Azure Active Directory 页（位于 [azure.microsoft.com/services/active-directory](https://azure.microsoft.com/services/active-directory)）。  

你和你的客户使用 Azure AD 在市场上进行身份验证。 你的客户在市场上选择你的试用列表后，系统会将其重定向到你的试用环境。  在你的试用环境中，你可以直接设置你的客户，而无需额外的登录步骤。 你的应用或产品/服务在身份验证过程中会收到来自 Azure AD 的令牌，其中包含用于在应用或产品/服务中创建用户帐户的有价值用户信息。 你可以自动执行设置，并增加转换的可能性。  
*   有关在身份验证过程中从 Azure AD 发送的令牌的详细信息，请访问“示例令牌”部分（位于 [docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)）

使用 Azure AD 为应用或试用启用一键式身份验证。  
*   简化从市场到试用的客户体验。  
*   保持“产品内体验”观感，即使是当用户从市场重定向到你的域或试用环境中时也是如此。  
*   降低用户在重定向时放弃的可能性，因为无需其他登录步骤。  
*   减少因 Azure AD 用户过多而产生的部署困难。  

## <a name="verify-your-azure-ad-integration-on-the-marketplace-multitenant-apps"></a>在市场上验证 Azure AD 集成：多租户应用  
使用 Azure AD 对解决方案支持以下选项。  
*   在市场的店面中注册应用。  
*   启用 Azure AD 中的多租户支持功能，以获取一键式试用体验。  
    *   有关应用注册的详细信息，请访问“将应用程序与 Azure Active Directory 集成”页（位于 [docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)）。  

如果你对使用 Azure AD 联合单一登录 (SSO) 不熟悉，请按照以下步骤操作。  
1.  在市场中注册应用。 
2.  使用 OAuth 2.0 或 OpenID Connect 开发 Azure AD 的 SSO。  
    *   有关 OAuth 2.0 的详细信息，请访问 OAuth 2.0 页（位于 [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)）。  
    *   有关 Open ID Connect 的详细信息，请访问 Open ID Connect 页（位于 [docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)）。  
3.  启用 Azure AD 中的多租户支持功能，以提供一键式试用体验。  
    *   有关 AppSource 认证的详细信息，请访问“AppSource 认证”页（位于 [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)）。 

## <a name="verify-your-azure-ad-integration-on-the-marketplace-single-tenant-apps"></a>在市场上验证 Azure AD 集成：单租户应用  
对单租户解决方案支持以下选项之一。  
*   使用 Azure AD B2B 将用户作为来宾用户添加到目录中。  
    *   有关 Azure AD B2B 的详细信息，请访问“什么是 Azure AD B2B 协作”页（位于 [docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)）。
*   通过“与我联系”手动为客户设置试用版。  
*   开发针对每个用户的体验版。  
*   使用 SSO 生成多租户示例演示应用。  

## <a name="next-steps"></a>后续步骤
*   访问 [Azure 市场和 AppSource 发布者指南](./marketplace-publishers-guide.md)页。  
 
---  

