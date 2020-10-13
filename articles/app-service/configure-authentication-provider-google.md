---
title: 配置 Google 身份验证
description: 了解如何将 Google authentication 配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519948"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>将应用服务或 Azure Functions 应用配置为使用 Google 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题说明如何将 Azure App Service 或 Azure Functions 配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register-your-application-with-google"></a><a name="register"> </a>向 Google 注册应用程序

1. 遵循 google [Sign-In](https://developers.google.com/identity/sign-in/web/server-side-flow) 上的 google 文档来创建客户端 ID 和客户端密钥。 无需进行任何代码更改。 只需使用以下信息：
    - 对于 **授权的 JavaScript**源，请 `https://<app-name>.azurewebsites.net` 在中将与应用名称一起使用 *\<app-name>* 。
    - 对于 **授权的重定向 URI**，请使用 `https://<app-name>.azurewebsites.net/.auth/login/google/callback` 。
1. 复制 "应用 ID" 和 "应用密钥" 值。

    > [!IMPORTANT]
    > 应用程序密钥是一个重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Google 信息

1. 在 [Azure 门户]中，转到你的应用服务应用。
1. 选择“设置” > “身份验证/授权”，并确保“应用服务身份验证”为“启用”。
1. 选择 " **Google**"，然后粘贴你之前获取的应用程序 ID 和应用程序密钥值。 启用应用程序所需的任何范围。
1. 选择“确定”  。

   应用服务提供身份验证，但不限制对站点内容和 Api 的授权访问。 有关详细信息，请参阅 [授权或拒绝用户](app-service-authentication-how-to.md#authorize-or-deny-users)。

1.  (可选) 将站点访问限制为仅限通过 Google 身份验证的用户访问，请将 " **请求未经身份验证时要执行的操作** " 设置为 " **google**"。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Google 进行身份验证。

    > [!CAUTION]
    > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”可能是首选，因此应用会以手动方式自行启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。

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

