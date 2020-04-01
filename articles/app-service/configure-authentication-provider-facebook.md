---
title: 配置 Facebook 身份验证
description: 了解如何将 Facebook 身份验证配置为应用服务或 Azure 功能应用的标识提供程序。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438021"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>将应用服务或 Azure 功能应用配置为使用 Facebook 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文演示如何将 Azure 应用服务或 Azure 功能配置为使用 Facebook 作为身份验证提供程序。

要完成本文中的过程，您需要一个 Facebook 帐户，该帐户具有经过验证的电子邮件地址和手机号码。 若要创建新的 Facebook 帐户，请转到 [facebook.com]。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>向 Facebook 注册应用程序

1. 转到[Facebook 开发人员]网站，并登录您的 Facebook 帐户凭据。

   如果您没有面向开发人员的 Facebook 帐户，请选择 **"开始"** 并按照注册步骤操作。
1. 选择 **"我的应用** > **添加新应用**"。
1. 在 **"显示名称"** 字段中：
   1. 键入应用的唯一名称。
   1. 提供您的**联系电子邮件**。
   1. 选择 **"创建应用 ID**"。
   1. 完成安全检查。

   将打开新 Facebook 应用的开发人员仪表板。
1. 选择**仪表板** > **Facebook 登录** > **设置** > **网络**。
1. 在**Facebook 登录**下的左侧导航中，选择 **"设置**"。
1. 在 **"有效 OAuth 重定向 URI"** 字段中`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`，输入 。 请记住，请`<app-name>`替换为 Azure 应用服务应用的名称。
1. 选择“保存更改”。****
1. 在左侧窗格中，选择 **"设置** > **基本**"。 
1. 在 **"应用机密"** 字段中，选择 **"显示**"。 复制**应用 ID** **和应用密钥**的值。 稍后使用它们在 Azure 中配置应用服务应用。

   > [!IMPORTANT]
   > 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
   >

1. 您用于注册应用程序的 Facebook 帐户是应用程序的管理员。 此时，只有管理员才能登录到此应用程序。

   要验证其他 Facebook 帐户，请选择 **"应用审阅**"并启用**\<"使应用名称>公开**"，使普通公众能够使用 Facebook 身份验证访问应用。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Facebook 信息

1. 登录到 Azure[门户]并导航到应用服务应用。
1. 选择 **"设置** > **身份验证/授权**"，并确保**应用服务身份验证****处于打开。**
1. 选择**Facebook**，然后粘贴到您以前获得的应用 ID 和应用机密值中。 启用应用程序所需的任何范围。
1. 选择“确定”  。

   ![移动应用 Facebook 设置的屏幕截图][0]

    默认情况下，应用服务提供身份验证，但不会限制对网站内容和 API 的授权访问。 您需要授权应用代码中的用户。
1. （可选）要仅限制通过 Facebook 身份验证的用户访问，请在未向**Facebook****验证请求时设置"操作**"。 设置此功能时，应用会要求对所有请求进行身份验证。 它还将所有未经身份验证的请求重定向到 Facebook 进行身份验证。

   > [!CAUTION]
   > 以这种方式限制访问适用于对应用的所有调用，对于主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。 对于此类应用程序，“允许匿名请求(无操作)”**** 可能是首选，因此应用会以手动方式自行启动身份验证。 有关详细信息，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 选择“保存”。 

现在，您已准备好在应用中使用 Facebook 进行身份验证。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 门户]: https://portal.azure.com/
