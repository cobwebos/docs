---
title: Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 16cb3e034bd928bbae8ae1f2e8d6b94b9ec699e4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302030"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 中库应用的 SAML 令牌中的高级证书签名选项
Azure Active Directory (Azure AD) 目前支持 Azure Active Directory 应用库中的数千个预先集成应用程序。 此数字包括使用 SAML 2.0 协议支持单一登录的 500 多个应用程序。 当用户通过 Azure AD 使用 SAML 对应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌已使用 Azure AD 中生成的唯一证书根据特定的标准算法签名。

Azure AD 对库应用程序使用一些默认设置。 默认值是根据应用程序的要求设置的。

Azure AD 支持高级证书签名设置。 若要选择这些选项，请先选中“显示高级证书签名设置”复选框：

![显示高级证书签名设置](./media/certificate-signing-options/saml-advance-certificate.png)

选中此复选框后，可以设置证书签名选项和证书签名算法。

## <a name="certificate-signing-options"></a>证书签名选项

Azure AD 支持三个证书签名选项：

* **为 SAML 断言签名**。 已针对大多数库应用程序设置此默认选项。 如果选择此选项，则用作 IdP 的 Azure AD 会使用应用程序的 X509 证书来为 SAML 断言和证书签名。 此外，它还使用“签名算法”下拉列表中选择的签名算法。

* **为 SAML 响应签名**。 如果选择此选项，则用作 IdP 的 Azure AD 会使用应用程序的 X509 证书来为 SAML 响应签名。 此外，它还使用“签名算法”下拉列表中选择的签名算法。

* **为 SAML 响应和断言签名**。 如果选择此选项，则用作 IdP 的 Azure AD 会使用应用程序的 X509 证书来为整个 SAML 令牌签名。 此外，它还使用“签名算法”下拉列表中选择的签名算法。

    ![证书签名选项](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>证书签名算法

Azure AD 支持使用两种签名算法来为 SAML 响应签名：

* **SHA-256**。 Azure AD 支持使用此默认算法来为 SAML 响应签名： 这是最新的算法，被认为比 SHA-1 更安全。 大多数应用程序支持 SHA-256 算法。 如果应用程序仅支持将 SHA-1 用作签名算法，可以更改算法。 否则，我们建议使用 SHA-256 算法来为 SAML 响应签名。

    ![SHA-256 证书签名算法](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**。 这是较旧的算法，被认为不如 SH-256 安全。 如果应用程序仅支持此签名算法，可以在“签名算法”下拉列表选择此选项。 然后，Azure AD 会使用 SHA-1 算法为 SAML 响应签名。

    ![SHA-1 证书签名算法](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>后续步骤
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [针对不在 Azure Active Directory 应用库中的应用程序配置单一登录](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [排查基于 SAML 的单一登录的问题](../develop/active-directory-saml-debugging.md)


