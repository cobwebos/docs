---
title: 配置 Google 身份验证
description: 了解如何将 Google 身份验证配置为应用服务应用的标识提供商。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670812"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>配置应用服务应用以使用 Google 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register-your-application-with-google"></a><a name="register"> </a>向 Google 注册应用程序

1. 按照[Google 登录服务器端应用](https://developers.google.com/identity/sign-in/web/server-side-flow)的文档创建客户端 ID 和客户端机密。 无需进行任何代码更改。 只需使用以下信息：
    - 对于**授权 JavaScript 源**，`https://<app-name>.azurewebsites.net`请与*\<应用名称>中的应用名称*一起使用。
    - 对于**授权重定向 URI，** 请使用`https://<app-name>.azurewebsites.net/.auth/login/google/callback`。
1. 复制应用 ID 和应用机密值。

    > [!IMPORTANT]
    > 应用密钥是一个重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Google 信息

1. 在 [Azure 门户]中，转到你的应用服务应用。
1. 选择 **"设置** > **身份验证/授权**"，并确保**应用服务身份验证****处于打开。**
1. 选择**Google**，然后粘贴到您以前获得的应用 ID 和应用机密值中。 启用应用程序所需的任何范围。
1. 选择“确定”。

   应用服务提供身份验证，但不限制对网站内容和 API 的授权访问。 有关详细信息，请参阅[授权或拒绝用户](app-service-authentication-how-to.md#authorize-or-deny-users)。

1. （可选）要仅限制用户访问 Google，请设置 **"操作"，当请求未**对**Google**进行身份验证时执行。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Google 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”**** 可能是首选，因此应用会以手动方式自行启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。****

现在，可以在应用中使用 Google 进行身份验证了。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 门户]: https://portal.azure.com/

