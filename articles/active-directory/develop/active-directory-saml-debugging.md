---
title: 如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录 | Microsoft Docs
description: '了解如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录 '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录

在调试基于 SAML 的应用程序集成时，使用 [Fiddler](http://www.telerik.com/fiddler) 之类的工具查看 SAML 请求和 SAML 响应（包含颁发给应用程序的 SAML 令牌）通常很有帮助。 

![Fiddler][1]

问题往往与 Azure Active Directory 或应用程序端的配置不当有关。 必须根据错误的出现位置，检查 SAML 请求或响应：

- 我在公司登录页中看到错误 [章节链接]
- 登录后我在应用程序页上看到错误 [章节链接]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>我在公司登录页中看到错误。

可以在[登录到配置为联合单一登录的库应用程序时出现的问题](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)中找到错误代码与解决方法步骤之间的一一对应描述。

如果在公司登录页中看到错误，则需要查看错误消息和 SAML 请求来调试问题。

### <a name="how-can-i-get-the-error--message"></a>如何获取错误消息？

若要获取错误消息，请查看页面右下角。 看到的错误包括：

- 关联 ID
- 时间戳
- 消息

![错误][2] 

 
关联 ID 和时间戳构成了唯一标识符，可以使用它来查找与登录失败问题相关的后端日志。 创建 Microsoft 支持案例时，这些值非常重要，因为它们可帮助工程师提供更快的问题解决方法。

消息陈述了登录问题的根本原因。 


### <a name="how-can-i-review-the-saml-request"></a>如何查看 SAML 请求？

应用程序发送到 Azure Active Directory 的 SAML 请求通常是来自 [https://login.microsoftonline.com](https://login.microsoftonline.com) 的最后一个 HTTP 200 响应。
 
在 Fiddler 中，可以通过选择此行，并在右侧面板中选择“检查器”>“WebForms”，来查看 SAML 请求。 右键单击“SAMLRequest”值并选择“发送到 TextWizard”。 然后在“转换”菜单中选择“从 Base64”以解码令牌并查看其内容。 

此外，还可以复制 SAMLrequest 中的值并使用另一个 Base64 解码器。

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

将 SAML 请求解码后，查看以下信息：

1. SAML 请求中的 **Destination** 对应于从 Azure Active Directory 获取的 SAML 单一登录服务 URL。
 
2. SAML 请求中的 **Issuer** 与在 Azure Active Directory 中为应用程序配置的“标识符”相同。 Azure AD 使用 Issuer 在目录中查找应用程序。

3. 应用程序预期在 **AssertionConsumerServiceURL** 中从 Azure Active Directory 接收 SAML 令牌。 可以在 Azure Active Directory 中配置此值，但如果此值是 SAML 请求的一部分，则不一定要这样做。


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>登录后我在应用程序页上看到错误

如果出现这种情况，表示应用程序未接受 Azure AD 发出的响应。 必须配合应用程序供应商来验证 Azure AD 发出的、包含 SAML 令牌的响应，以确定缺少或错误的内容。 

### <a name="how-can-i-get-and-review-the-saml-response"></a>如何获取和查看 SAML 响应？

包含 SAML 令牌的 Azure AD 响应通常是在从 https://login.microsoftonline.com 发出 HTTP 302 重定向之后发生的响应，它将发送到应用程序的已配置“回复 URL”。 

可以通过选择此行，并在右窗格中选择“检查器”>“WebForms”，来查看 SAML 令牌。 右键单击“SAMLResponse”值并选择“发送到 TextWizard”。 然后在“转换”菜单中选择“从 Base64”以解码令牌，并使用另一个 Base64 解码器查看其内容。 

还可以复制 **SAMLrequest** 中的值并使用另一个 Base64 解码器。

请访问[登录后应用程序页中出现错误](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)故障排除指南，详细了解 SAML 响应中可能缺少或错误的内容。

有关如何查看 SAML 响应的信息，请访问[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response)一文。


## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [如何为预先集成的应用自定义 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
