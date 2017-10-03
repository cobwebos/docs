---
title: "Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 中库应用的 SAML 令牌中的高级证书签名选项
Azure Active Directory 目前支持 Azure AD 应用库中数千个预先集成的应用程序，其中有 500 个以上的应用程序支持使用 SAML 2.0 协议的单一登录。 当用户通过 Azure AD 使用 SAML 对应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌是使用 Azure AD 中生成的唯一证书签名的。 此 SAML 令牌是使用特定的标准算法签名的。

Azure Active Directory 对库应用程序使用一些默认设置。 根据应用程序要求设置默认值。

Azure Active Directory 支持高级证书签名设置。 若要选择这些选项，首先请按如下所示选中“SAML 高级证书签名设置”复选框。

![证书签名选项][1]

选中此复选框后，可以设置“证书签名选项”和“证书签名算法”。

## <a name="certificate-signing-options"></a>证书签名选项

下面是 Azure AD 支持的三种证书签名选项。

1. **签名 SAML 断言** - 这是针对大多数库应用程序设置的默认选项。 如果选择此选项，则用作 IDP 的 Azure AD 会使用应用程序的 X509 证书来为 SAML 断言和证书签名。 此外，它还使用下面的下拉列表中选择的签名算法。

2. **签名 SAML 响应** - 如果选择此选项，则用作 IDP 的 Azure AD 会使用应用程序的 X509 证书来为 SAML 响应签名。 此外，它还使用下面的下拉列表中选择的签名算法。

3. **签名 SAML 响应和断言** - 如果选择此选项，则用作 IDP 的 Azure AD 会使用应用程序的 X509 证书来为整个 SAML 令牌签名。 此外，它还使用下面的下拉列表中选择的签名算法。

    ![证书签名选项][4]

## <a name="certificate-signing-algorithm"></a>证书签名算法

Azure Active Directory 支持使用两种签名算法来为 SAML 响应签名。

1. SHA256 - 这是默认算法，Azure Active Directory 使用它来为 SAML 响应签名。 这是最新的算法，被认为比 SHA1 更安全。 大多数应用程序支持 SHA256 算法。 如果应用程序仅支持 Sha1 作为签名算法，可以更改应用程序。 否则，我们建议使用 SHA256 算法来为 SAML 响应签名。

    ![SHA256 证书签名算法][3]

2. SHA1 - 这是较旧的算法，被认为不够安全。 如果应用程序仅支持此签名算法，可以在下拉列表选择此选项。 选择此选项时，Azure AD 会使用 Sha1 算法为 SAML 响应签名。

    ![SHA1 证书签名算法][2]

## <a name="next-steps"></a>后续步骤
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](active-directory-saas-custom-apps.md)
* [排查基于 SAML 的单一登录问题](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
