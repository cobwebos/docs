---
title: "Azure Active Directory 身份验证协议 | Microsoft 文档"
description: "概述 Azure Active Directory (AD) 支持的身份验证协议"
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: e106a9bdf28243dd829b6a014b73c148809c1bde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# Azure Active Directory 身份验证协议
Azure Active Directory (Azure AD) 支持多个最广泛使用的身份验证和授权协议。 本部分中的主题介绍 Azure AD 中支持的协议及其实现。 这些主题包括支持的声明类型的回顾、联合元数据的使用简介、详细的 OAuth 2.0。 和 SAML 2.0 协议参考文档，以及故障排除部分。

## 身份验证协议文章和参考
* [有关 Azure AD 中签名密钥滚动更新的重要信息](active-directory-signing-key-rollover.md) - 了解 Azure AD 的签名密钥滚动更新频率、可以进行的自动更新密钥的更改，以及针对如何更新最常见的应用程序方案的讨论。
* [支持的令牌和声明类型](active-directory-token-and-claims.md) - 了解 Azure AD 颁发的令牌中的声明。
* [联合元数据](active-directory-federation-metadata.md) - 了解如何查找和解释 Azure AD 生成的元数据文档。
* [Azure AD 中的 OAuth 2.0](active-directory-protocols-oauth-code.md) - 了解 OAuth 2.0 在 Azure AD 中的实现。
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) - 了解如何使用 OAuth 2.0（一种授权协议）进行身份验证。
* [使用客户端凭据的服务间调用](active-directory-protocols-oauth-service-to-service.md) - 了解如何对服务到服务调用使用 OAuth 2.0 客户端凭据授权流。
* [使用代理流的服务间调用](active-directory-protocols-oauth-on-behalf-of.md) - 了解如何对服务到服务调用使用 OAuth 2.0 代理流。
* [SAML 协议参考](active-directory-saml-protocol-reference.md) - 了解 Azure AD 的单一登录和单一注销 SAML 配置文件。

## 另请参阅
[Azure Active Directory 开发人员指南](active-directory-developers-guide.md)

[Active Directory 代码示例](active-directory-code-samples.md)
