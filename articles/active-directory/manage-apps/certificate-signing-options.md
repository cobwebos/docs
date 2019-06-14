---
title: Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1baf4af41fd5dcb6723b6ee2827ae91b43b072d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780962"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 中库应用的 SAML 令牌中的高级证书签名选项

Azure Active Directory (Azure AD) 目前支持 Azure Active Directory 应用库中的数千个预先集成应用程序。 通过使用单一登录支持 500 个以上的应用程序[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)(SAML) 2.0 协议，如[NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite)应用程序。 当客户使用 SAML 向应用程序通过 Azure AD 身份验证时，Azure AD 会将令牌发送到应用程序 （通过 HTTP POST)。 然后，应用程序验证，并使用该令牌来登录而不是提示输入用户名和密码的客户。 这些 SAML 令牌已使用 Azure AD 中生成的唯一证书根据特定的标准算法签名。

Azure AD 对库应用程序使用一些默认设置。 默认值是根据应用程序的要求设置的。

在 Azure AD 中，您可以设置证书签名选项和证书签名算法。

## <a name="certificate-signing-options"></a>证书签名选项

Azure AD 支持三个证书签名选项：

* **为 SAML 断言签名**。 已针对大多数库应用程序设置此默认选项。 如果选择此选项时，Azure AD 作为标识提供者 (IdP) 进行签名的 SAML 断言和证书[X.509](https://wikipedia.org/wiki/X.509)的应用程序的证书。

* **为 SAML 响应签名**。 如果选择此选项，作为 IdP 的 Azure AD 会对使用应用程序的 X.509 证书为 SAML 响应进行签名。

* **为 SAML 响应和断言签名**。 如果选择此选项，作为 IdP 的 Azure AD 对与应用程序的 X.509 证书的整个 SAML 令牌进行签名。

## <a name="certificate-signing-algorithms"></a>证书签名算法

Azure AD 支持两种签名算法或安全哈希算法 (Sha)，来为 SAML 响应签名：

* **SHA-256**。 Azure AD 支持使用此默认算法来为 SAML 响应签名： 它是最新的算法，比 sha-1 更安全。 大多数应用程序支持 SHA-256 算法。 如果应用程序仅支持将 SHA-1 用作签名算法，可以更改算法。 否则，我们建议使用 SHA-256 算法来为 SAML 响应签名。

* **SHA-1**。 此算法是更早版本，并将其视为小于比 SHA-256 安全。 如果应用程序仅支持此签名算法，可以在“签名算法”下拉列表选择此选项。  然后，Azure AD 会使用 SHA-1 算法为 SAML 响应签名。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>更改证书签名选项和签名算法

若要更改应用程序的 SAML 证书签名选项和证书签名算法，请选择相关应用程序：

1. 在中[Azure Active Directory 门户](https://aad.portal.azure.com/)，登录到你的帐户。 此时会显示“Azure Active Directory 管理中心”页。 
1. 在左窗格中，选择“企业应用程序”  。 将显示你的帐户中的企业应用程序的列表。
1. 选择一个应用程序。 应用程序的概览页会显示。

   ![应用程序概述页](./media/certificate-signing-options/application-overview-page.png)

接下来，更改证书签名的该应用程序的 SAML 令牌中的选项：

1. 在应用程序概述页的左窗格中，选择**单一登录**。

2. 如果**设置了单一登录使用 SAML-预览**页出现后，转到步骤 5。

3. 如果**选择单一登录方法**不显示页中，选择**更改单一登录模式**可显示该页。

4. 在中**选择单一登录方法**页上，选择**SAML**如果可用。 (如果**SAML**不是可用，应用程序不支持 SAML、 和，则可以忽略此过程和文章的其余部分。)

5. 在中**设置了单一登录使用 SAML-预览**页上，找到**SAML 签名证书**标题，然后选择**编辑**图标 （铅笔）。 **SAML 签名证书**页将出现。

   ![SAML 签名页](./media/certificate-signing-options/saml-signing-page.png)

6. 在**签名选项**下拉列表中，选择**签名 SAML 响应**，**登录 SAML 断言**，或者**签名 SAML 响应和断言**。 在本文前面部分中显示这些选项的说明[证书签名选项](#certificate-signing-options)。

7. 在中**签名算法**下拉列表中，选择**sha-1**或**SHA-256**。 在本文前面部分中显示这些选项的说明[证书签名算法](#certificate-signing-algorithms)部分。

8. 如果您感到满意所做的选择，选择**保存**要应用的新 SAML 签名证书设置。 否则，请选择**X**放弃所做的更改。

## <a name="next-steps"></a>后续步骤

* [针对不在 Azure Active Directory 应用库中的应用程序配置单一登录](configure-federated-single-sign-on-non-gallery-applications.md)
* [排查基于 SAML 的单一登录的问题](../develop/howto-v1-debug-saml-sso-issues.md)
