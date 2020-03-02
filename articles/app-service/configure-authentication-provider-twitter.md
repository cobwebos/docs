---
title: 配置 Twitter 身份验证
description: 了解如何将 Twitter 身份验证配置为应用服务应用的标识提供者。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207127"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>将应用服务应用配置为使用 Twitter 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure App Service 配置为使用 Twitter 作为身份验证提供程序。

若要完成本文中的过程，需要一个包含已验证电子邮件地址和电话号码的 Twitter 帐户。 若要创建新的 Twitter 帐户，请转至 [twitter.com]。

## <a name="register"></a>向 Twitter 注册应用程序

1. 登录到[Azure 门户]并中转到你的应用程序。 复制 **URL**。 你将使用它来配置你的 Twitter 应用。
1. 请前往[Twitter 开发人员]网站，使用你的 Twitter 帐户凭据登录，然后选择 "**创建应用**"。
1. 输入新应用的**应用名称**和**应用程序说明**。 将应用程序的**url**粘贴到 "**网站 url** " 字段。 在 "**回调 url** " 部分中，输入应用服务应用的 HTTPS URL，并将路径追加 `/.auth/login/twitter/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/twitter/callback` 。
1. 在页面底部的 "**告诉我们如何使用此应用**" 中键入至少100个字符，然后选择 "**创建**"。 在弹出窗口中再次单击 "**创建**"。 将显示应用程序详细信息。
1. 选择“密钥和访问令牌”选项卡。

   记下以下值：
   - API 密钥
   - API 密钥

   > [!NOTE]
   > API 密钥是重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

## <a name="secrets"></a>将 Twitter 信息添加到应用程序

1. 在[Azure 门户]中转到你的应用程序。
1. 选择 "**设置**" > "**身份验证/授权**"，并确保 "**应用服务身份验证** **" 已打开**。
1. 选择 " **Twitter**"。
1. 粘贴你之前获取的 `API key` 和 `API secret key` 值。
1. 选择“确定”。

   ![移动应用 Twitter 设置的屏幕截图][1]

   默认情况下，应用服务提供身份验证，但不限制对站点内容和 Api 的授权访问。 必须在应用代码中为用户授权。

1. （可选）要限制只有通过 Twitter 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Twitter”。 设置此功能时，应用要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Twitter 进行身份验证。

   > [!CAUTION]
   > 以这种方式限制访问权限适用于对应用的所有调用，对于具有公开可用主页的应用，与在许多单页应用程序中一样，这可能并不理想。 对于此类应用程序，"**允许匿名请求（无操作）** " 可能首选，以便应用手动启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。

现在，可以在应用中使用 Twitter 进行身份验证了。

## <a name="related-content"></a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 开发人员]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 门户]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
