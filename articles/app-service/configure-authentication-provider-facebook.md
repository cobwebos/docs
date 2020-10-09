---
title: 配置 Facebook 身份验证
description: 了解如何配置 Facebook 身份验证作为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519963"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>将应用服务或 Azure Functions 应用配置为使用 Facebook 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 或 Azure Functions 配置为将 Facebook 用作身份验证提供程序。

若要完成本文中的过程，需要一个包含已验证电子邮件地址和移动电话号码的 Facebook 帐户。 若要创建新的 Facebook 帐户，请转到 [facebook.com]。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>向 Facebook 注册应用程序

1. 请切换到 [Facebook 开发人员] 网站，并以 facebook 帐户凭据登录。

   如果你没有 Facebook for 开发人员帐户，请选择 " **入门** "，然后按照注册步骤操作。
1. 选择 **"我的应用" "**  >  **添加新应用**"。
1. 在 " **显示名称** " 字段中：
   1. 为应用键入唯一的名称。
   1. 提供您的 **联系人电子邮件**。
   1. 选择“创建应用 ID”。
   1. 完成安全检查。

   此时将打开新 Facebook 应用的开发人员仪表板。
1. 选择 "**仪表板**  >  **Facebook 登录**" "  >  **设置**  >  **Web**"。
1. 在左侧导航中的 " **Facebook 登录**" 下，选择 " **设置**"。
1. 在 " **有效的 OAuth 重定向 uri** " 字段中，输入 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` 。 请记得将替换为 `<app-name>` 您的 Azure App Service 应用程序的名称。
1. 选择“保存更改”。
1. 在左窗格中，选择 "**设置**" "  >  **基本**"。 
1. 在 " **应用密码** " 字段中，选择 " **显示**"。 复制 " **应用 ID** " 和 " **应用机密**" 的值。 稍后可将其用于配置 Azure 中的应用服务应用。

   > [!IMPORTANT]
   > 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
   >

1. 用于注册应用程序的 Facebook 帐户是应用的管理员。 此时，只有管理员才能登录到此应用程序。

   若要对其他 Facebook 帐户进行身份验证，请选择 " **应用检查** "，并启用 " ** \<your-app-name> 公开** " 以使用 Facebook 身份验证来访问应用，以启用常规公共。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Facebook 信息

1. 登录到 [Azure 门户] 并导航到应用服务应用。
1. 选择“设置” > “身份验证/授权”，并确保“应用服务身份验证”为“启用”。
1. 选择 " **Facebook**"，然后粘贴你之前获取的应用程序 ID 和应用程序密钥值。 启用应用程序所需的任何范围。
1. 选择“确定”  。

   ![移动应用 Facebook 设置的屏幕截图][0]

    默认情况下，应用服务提供身份验证，但它不限制对站点内容和 Api 的授权访问。 你需要在应用程序代码中对用户进行授权。
1.  (可选) ，以将访问权限限制为仅限通过 Facebook 身份验证的用户访问，请设置**在请求未通过 facebook 身份验证时要执行的操作**。 **Facebook** 设置此功能时，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Facebook 进行身份验证。

   > [!CAUTION]
   > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”可能是首选，因此应用会以手动方式自行启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。

现在，你可以在应用中使用 Facebook 进行身份验证。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 门户]: https://portal.azure.com/
