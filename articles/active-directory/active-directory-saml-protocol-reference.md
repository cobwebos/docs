---
title: Azure AD SAML 协议参考 | Microsoft Docs
description: 本文概述 Azure Active Directory 中的单一登录和单一注销 SAML 配置文件。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
ms.locfileid: "26615770"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Azure Active Directory 如何使用 SAML 协议
Azure Active Directory (Azure AD) 使用 SAML 2.0 协议，使应用程序能够为其用户提供单一登录体验。 Azure AD 的[单一登录](active-directory-single-sign-on-protocol-reference.md)和[单一注销](active-directory-single-sign-out-protocol-reference.md) SAML 配置文件说明了如何在标识提供者服务中使用 SAML 断言、协议和绑定。

SAML 协议要求标识提供者 (Azure AD) 与服务提供者（应用程序）交换有关自身的信息。

将应用程序注册到 Azure AD 时，应用开发人员需将联合身份验证相关的信息注册到 Azure AD。 这包括应用程序的**重定向 URI**。

Azure Active Directory 公开特定于租户的和公用的（独立于租户的）单一登录和单一注销终结点。 这些 URL 表示可寻址位置（不只是标识符），方便你转到终结点读取元数据。

* 特定于租户的终结点位于 `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`。  <TenantDomainName> 占位符表示 Azure AD 租户的已注册域名或 TenantID GUID。 例如，contoso.com 租户的联合元数据位于：https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* 独立于租户的终结点位于 `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`。此终结点地址中显示 **common**，而不是租户域名或 ID。

有关 Azure AD 发布的联合元数据文档的信息，请参阅 [Federation Metadata](active-directory-federation-metadata.md)（联合元数据）。
