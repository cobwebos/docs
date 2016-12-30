---
title: "Azure Active Directory 身份验证协议 | Microsoft 文档"
description: "概述 Azure Active Directory (AD) 支持的身份验证协议"
documentationcenter: dev-center-name
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f0b3afb1163d4ba728755b236fd685184d464cd3


---
# <a name="azure-active-directory-authentication-protocols"></a>Azure Active Directory 身份验证协议
Azure Active Directory (Azure AD) 支持多个最广泛使用的身份验证和授权协议。 本部分中的主题介绍 Azure AD 中支持的协议及其实现。 这些主题包括支持的声明类型的回顾、联合元数据的使用简介、详细的 OAuth 2.0。 和 SAML 2.0 协议参考文档，以及故障排除部分。

## <a name="authentication-protocols-articles-and-reference"></a>身份验证协议文章和参考
* [有关 Azure AD 中签名密钥滚动更新的重要信息](active-directory-signing-key-rollover.md) - 了解 Azure AD 的签名密钥滚动更新频率、你可以进行的自动更新密钥的更改，以及针对如何更新最常见的应用程序方案的讨论。
* [支持的令牌和声明类型](active-directory-token-and-claims.md)：了解 Azure AD 颁发的令牌中的声明。
* [联合元数据](https://msdn.microsoft.com/library/azure/dn195592.aspx) - 了解如何查找和解释 Azure AD 生成的元数据文档。
* [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) - 了解 OAuth 2.0 在 Azure AD 中的实现。
* [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) - 了解如何使用 OAuth 2.0（一种授权协议）进行身份验证。
* [SAML 协议参考](https://msdn.microsoft.com/library/azure/dn195591.aspx) - 了解 Azure AD 的单一登录和单一注销 SAML 配置文件。
* [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) - 了解 Azure AD 中的 WS-Federation 1.2。
* [身份验证协议故障排除](https://msdn.microsoft.com/library/azure/dn195584.aspx) - 了解如何防止问题以及解释并解决在使用 Azure AD 时遇到的错误。

## <a name="see-also"></a>另请参阅
[Azure Active Directory 开发人员指南](active-directory-developers-guide.md)

[使用 Azure AD 进行身份验证](../app-service-web/web-sites-authentication-authorization.md)

[Active Directory 代码示例](active-directory-code-samples.md)




<!--HONumber=Dec16_HO5-->


