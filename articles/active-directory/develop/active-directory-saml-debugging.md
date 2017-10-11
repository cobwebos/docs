---
title: "如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录 "
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 31447d597296bac57481dc2acb4a95ee3a104161
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录
在调试基于 SAML 的应用程序集成时，使用 [Fiddler](http://www.telerik.com/fiddler) 之类的工具查看 SAML 请求、SAML 响应和颁发给应用程序的实际 SAML 令牌通常很有帮助。 通过检查 SAML 令牌，可以确保按预期传递所有所需的属性、SAML 主题中的用户名和颁发者 URI。

![][1]

包含 SAML 令牌的 Azure AD 响应通常是在从 https://login.windows.net 发出 HTTP 302 重定向之后发生的响应，它将发送到应用程序的已配置**回复 URL**。 

可以通过选择此行，并在右窗格中选择“检查器”>“WebForms”，来查看 SAML 令牌。 右键单击“SAMLResponse”值并选择“发送到 TextWizard”。 然后在“转换”菜单中选择“从 Base64”，解码令牌并查看其内容。


            **注意**：查看此 HTTP 请求的内容时，Fiddler 可能会提示配置 HTTPS 流量解密，此时需要执行此操作。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](../active-directory-saas-custom-apps.md)
* [如何为预先集成的应用自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png