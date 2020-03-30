---
title: Azure AD 应用的高级 SAML 令牌证书签名选项
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
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159193"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 中库应用的 SAML 令牌中的高级证书签名选项

Azure Active Directory (Azure AD) 目前支持 Azure Active Directory 应用库中的数千个预先集成应用程序。 超过 500 个应用程序使用[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML） 2.0 协议（如[NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite)应用程序）支持单一登录。 当客户使用 SAML 通过 Azure AD 对应用程序进行身份验证时，Azure AD 会向应用程序发送令牌（通过 HTTP POST）。 然后，应用程序验证并使用令牌登录客户，而不是提示用户名和密码。 这些 SAML 令牌已使用 Azure AD 中生成的唯一证书根据特定的标准算法签名。

Azure AD 对库应用程序使用一些默认设置。 默认值是根据应用程序的要求设置的。

在 Azure AD 中，可以设置证书签名选项和证书签名算法。

## <a name="certificate-signing-options"></a>证书签名选项

Azure AD 支持三个证书签名选项：

* **为 SAML 断言签名**。 已针对大多数库应用程序设置此默认选项。 如果选择此选项，Azure AD 作为标识提供程序 （IdP） 使用应用程序的[X.509](https://wikipedia.org/wiki/X.509)证书对 SAML 断言和证书进行签名。

* **为 SAML 响应签名**。 如果选择此选项，Azure AD 作为 IdP 使用应用程序的 X.509 证书对 SAML 响应进行签名。

* **为 SAML 响应和断言签名**。 如果选择此选项，Azure AD 作为 IdP 使用应用程序的 X.509 证书对整个 SAML 令牌进行签名。

## <a name="certificate-signing-algorithms"></a>证书签名算法

Azure AD 支持两个签名算法，或安全哈希算法 （SHA） 对 SAML 响应进行签名：

* **SHA-256**. Azure AD 支持使用此默认算法来为 SAML 响应签名： 它是最新的算法，比SHA-1更安全。 大多数应用程序支持 SHA-256 算法。 如果应用程序仅支持将 SHA-1 用作签名算法，可以更改算法。 否则，我们建议使用 SHA-256 算法来为 SAML 响应签名。

* **SHA-1**. 此算法较旧，并且被视为不如 SHA-256 安全。 如果应用程序仅支持此签名算法，可以在“签名算法”下拉列表选择此选项。**** 然后，Azure AD 会使用 SHA-1 算法为 SAML 响应签名。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>更改证书签名选项和签名算法

要更改应用程序的 SAML 证书签名选项和证书签名算法，请选择有问题的应用程序：

1. 在[Azure 活动目录门户](https://aad.portal.azure.com/)中，登录到您的帐户。 此时会显示“Azure Active Directory 管理中心”页。****
1. 在左窗格中，选择“企业应用程序”****。 将显示帐户中的企业应用程序列表。
1. 选择一个应用程序。 将显示应用程序的概述页。

   ![示例：应用程序概述页](./media/certificate-signing-options/application-overview-page.png)

接下来，更改该应用程序的 SAML 令牌中的证书签名选项：

1. 在应用程序概述页的左侧窗格中，选择 **"单一登录**"。
1. 如果出现"**设置带有 SAML 的单个登录 - 预览**"页，请转到步骤 5。
1. 如果未显示 **"选择单个登录方法**页"，请选择 **"更改单一登录模式**"以显示该页。
1. 在 **"选择单个登录方法**"页中，选择**SAML（** 如果可用）。 （如果**SAML**不可用，则应用程序不支持 SAML，并且可能会忽略此过程和文章的其余部分。
1. 在 **"使用 SAML 设置单个登录 - 预览**"页中，找到**SAML 签名证书**标题并选择 **"编辑"** 图标（铅笔）。 将显示**SAML 签名证书**页。

   ![示例：SAML 签名证书页](./media/certificate-signing-options/saml-signing-page.png)

1. 在“签名选项”下拉列表中，选择“为 SAML 响应签名”、“为 SAML 断言签名”或“为 SAML 响应和断言签名”。**************** 这些选项的说明出现在本文前面的[证书签名选项](#certificate-signing-options)中。
1. 在“签名算法”下拉列表中，选择“SHA-1”或“SHA-256”。************ 这些选项的说明在本文前面显示在[证书签名算法](#certificate-signing-algorithms)部分。
1. 如果您对选择感到满意，请选择"**保存"** 以应用新的 SAML 签名证书设置。 否则，选择**X**以丢弃更改。

## <a name="next-steps"></a>后续步骤

* [针对不在 Azure Active Directory 应用库中的应用程序配置单一登录](configure-federated-single-sign-on-non-gallery-applications.md)
* [排查基于 SAML 的单一登录的问题](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
