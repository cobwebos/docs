---
title: 配置 Twitter 身份验证
description: 了解如何将 Twitter 身份验证配置为应用服务或 Azure 功能应用的标识提供程序。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 3f85f30e0a64b6e39b905fc05503a445aa5876ba
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437995"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>将应用服务或 Azure 功能应用配置为使用 Twitter 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文演示如何将 Azure 应用服务或 Azure 函数配置为使用 Twitter 作为身份验证提供程序。

要完成本文中的过程，您需要一个具有已验证电子邮件地址和电话号码的 Twitter 帐户。 若要创建新的 Twitter 帐户，请转至 [twitter.com]。

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>向 Twitter 注册应用程序

1. 登录到 Azure[门户]并转到应用程序。 复制 **URL**。 您将使用它来配置您的 Twitter 应用。
1. 转到[Twitter 开发人员]网站，使用 Twitter 帐户凭据登录，然后选择"**创建应用**"。
1. 输入新**应用的应用名称**和**应用程序说明**。 将应用程序的**URL**粘贴到 **"网站 URL"** 字段中。 在 **"回调 URL"** 部分中，输入应用服务应用的 HTTPS URL 并追加路径`/.auth/login/twitter/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/twitter/callback` 。
1. 在页面底部，在 **"告诉我们如何使用此应用程序**"中键入至少 100 个字符，然后选择"**创建**"。 在弹出窗口中再次单击 **"再次创建**"。 将显示应用程序详细信息。
1. 选择“密钥和访问令牌”选项卡****。

   记下这些值：
   - API 密钥
   - API 密钥

   > [!NOTE]
   > API 密钥是一个重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Twitter 信息

1. 在 [Azure 门户]中转到你的应用程序。
1. 选择 **"设置** > **身份验证/授权**"，并确保**应用服务身份验证****处于打开。**
1. 选择**推特**。
1. 粘贴之前`API key`获得的`API secret key`和 值。
1. 选择“确定”  。

   ![移动应用推特设置的屏幕截图][1]

   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。

1. （可选）要限制只有通过 Twitter 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”**** 设置为“Twitter”****。 设置此功能时，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Twitter 进行身份验证。

   > [!CAUTION]
   > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”**** 可能是首选，因此应用会以手动方式自行启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。 

现在，可以在应用中使用 Twitter 进行身份验证了。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 开发人员]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 门户]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
