---
title: 使用自定义策略进行电话注册和登录（预览版）
titleSuffix: Azure AD B2C
description: 使用 Azure 活动目录 B2C 中的自定义策略，在文本消息中向应用程序用户的电话发送一次性密码 （OTP）。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183952"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>在 Azure AD B2C（预览）中使用自定义策略设置电话注册和登录

在 Azure 活动目录 B2C（Azure AD B2C） 中使用手机注册和登录，使用户能够使用一次性密码 （OTP） 向其手机发送短信来注册并登录到应用程序。 一次性密码有助于将用户忘记或泄露其密码的风险降至最低。

按照本文中的步骤使用自定义策略，使您的客户能够使用发送到其手机的一次性密码注册并登录到您的应用程序。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>定价

使用 SMS 短信向用户发送一次性密码，并且可能会因发送的每条消息向您收费。 有关定价信息，请参阅[Azure 活动目录 B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的**单独费用**部分。

## <a name="prerequisites"></a>先决条件

在设置 OTP 之前，您需要提供以下资源。

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的 Web 应用程序](tutorial-register-applications.md)
* 上载到租户的[自定义策略](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>获取电话注册&登录入门包

首先更新电话注册和登录自定义策略文件，以便与 Azure AD B2C 租户一起工作。

以下步骤假定您已完成[先决条件](#prerequisites)，并已将[自定义策略初学者包存储库][starter-pack]克隆到本地计算机。

1. 在启动包回购的本地克隆中查找[电话注册和登录自定义策略文件][starter-pack-phone]，或直接下载这些文件。 XML 策略文件位于以下目录中：

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. 在每个文件中，将字符串`yourtenant`替换为 Azure AD B2C 租户的名称。 例如，如果 B2C 租户的名称为*contosob2c，* 则 所有实例`yourtenant.onmicrosoft.com`都变为`contosob2c.onmicrosoft.com`。

1. 完成"[将应用程序标识添加到 Azure](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [活动目录 B2C 中的自定义策略](custom-policy-get-started.md)"开始"的自定义策略部分中的步骤。 在这种情况下，使用在完成先决条件`/phone-number-passwordless/`**`Phone_Email_Base.xml`**"*身份体验框架*"和 *"代理身份体验框架*"时注册的两个应用程序（**客户端）标识**进行更新。

## <a name="upload-the-policy-files"></a>上传策略文件

1. 登录到 Azure[门户](https://portal.azure.com)并导航到 Azure AD B2C 租户。
1. 在“策略”下，选择“Identity Experience Framework”。********
1. 选择 **"上载自定义策略**"。
1. 按以下顺序上载策略文件：
    1. *Phone_Email_Base.xml*
    1. *注册人signinin与Phone.xml*
    1. *注册人signin与电话或电子邮件.xml*
    1. *配置文件编辑电话仅.xml*
    1. *配置文件编辑PhoneEmail.xml*
    1. *更改电话号码.xml*
    1. *密码重置电子邮件.xml*

上载每个文件时，Azure 会添加前`B2C_1A_`缀 。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在**自定义策略**下，选择**B2C_1A_SignUpOrSignInWithPhone。**
1. 在 **"选择应用程序"** 下，选择在完成先决条件时注册的*Webapp1*应用程序。
1. 对于**选择答复 URL，** 请选择`https://jwt.ms`。
1. 选择 **"立即运行**"并使用电子邮件地址或电话号码注册。
1. 选择 **"立即运行"，** 然后使用相同的帐户登录，以确认配置正确。

## <a name="get-user-account-by-phone-number"></a>按电话号码获取用户帐户

使用电话号码注册但不提供恢复电子邮件地址的用户记录在 Azure AD B2C 目录中，其电话号码为其登录名称。 如果用户然后想要更改其电话号码，您的帮助台或支持团队必须首先找到他们的帐户，然后更新其电话号码。

您可以使用[Microsoft 图形](manage-user-accounts-graph-api.md)按用户的电话号码（登录名称）查找用户：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

例如：

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>后续步骤

您可以在 GitHub 上找到电话注册和登录自定义策略初学者包（和其他初学者包）：

[Azure-采样/主动目录-b2c-自定义策略启动包/方案/电话号无密码][starter-pack-phone]

入门包策略文件使用多重身份验证技术配置文件和电话号码声明转换：

* [定义 Azure 多重身份验证技术配置文件](multi-factor-auth-technical-profile.md)
* [定义电话号码声明转换](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
