---
title: Azure AD 使用 SAML 协议的方式 | Microsoft Docs
description: 本文概述 Azure Active Directory 中的单一登录和单一注销 SAML 配置文件。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57c5f21e04b2d3adad975be0368d9435583b4f72
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844663"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Azure AD 使用 SAML 协议的方式

Azure Active Directory (Azure AD) 使用 SAML 2.0 协议，使应用程序能够为其用户提供单一登录体验。 Azure AD 的[单一登录](single-sign-on-saml-protocol.md)和[单一注销](single-sign-out-saml-protocol.md) SAML 配置文件说明了如何在标识提供者服务中使用 SAML 断言、协议和绑定。

SAML 协议要求标识提供者 (Azure AD) 与服务提供者（应用程序）交换有关自身的信息。

将应用程序注册到 Azure AD 时，应用开发人员需将联合身份验证相关的信息注册到 Azure AD。 这些信息包括应用程序的**重定向 URI** 和**元数据 URI**。

Azure AD 使用云服务的**元数据 URI** 来检索签名密钥和注销 URI。 客户可以在“Azure AD”->“应用注册”中打开应用，然后可以在“设置”->“属性”中更新注销 URL。 这样，Azure AD 可以将响应发送到正确的 URL。 

Azure Active Directory 公开特定于租户的和公用的（独立于租户的）单一登录和单一注销终结点。 这些 URL 表示可寻址位置（不只是标识符），方便你转到终结点读取元数据。

* 特定于租户的终结点位于 `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`。 *\<TenantDomainName >* 占位符表示 Azure AD 租户的已注册域名或 TenantID GUID。 例如，contoso.com 租户的联合元数据位于： https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* 独立于租户的终结点位于 `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`。 此终结点地址中显示 **common**，而不是租户域名或 ID。

有关 Azure AD 发布的联合元数据文档的信息，请参阅 [Federation Metadata](azure-ad-federation-metadata.md)（联合元数据）。
