---
title: 如何为应用服务应用程序配置 Facebook 身份验证
description: 了解如何为应用服务应用程序配置 Facebook 身份验证。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 1d2b294fc0663770f9a699e300672695225dfdfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152085"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>如何将应用服务应用程序配置为使用 Facebook 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍了如何将 Azure 应用服务配置为使用 Facebook 作为验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Facebook 帐户和一个手机号码。 若要创建新的 Facebook 帐户，请转到 [facebook.com]。

## <a name="register"> </a>向 Facebook 注册应用程序
1. 登录到 [Azure 门户]并导航到应用程序。 复制 **URL**。 在配置 Facebook 应用时会用到此信息。
2. 在其他浏览器窗口中，导航到 [Facebook Developers]（Facebook 开发者）网站，并使用 Facebook 帐户凭据登录。
3. （可选）如果尚未注册，请单击“应用” > “以开发者身份注册”，接受用户政策，并遵照注册步骤操作。
4. 单击“我的应用” > “添加新应用”。
5. 在**显示名称**中，键入应用的唯一名称。 此外，还提供**联系人电子邮件**，然后单击“创建应用 ID”并完成安全检查。 这会转到新 Facebook 应用的开发人员仪表板。
7. 在 **Facebook 登录**下，单击“设置”，在 **Facebook 登录**下左侧导航栏中选择“设置”。
8. 将应用程序的“重定向 URI”添加到“有效的 OAuth 重定向 URI”中，然后单击“保存更改”。
   
   > [!NOTE]
   > 重定向 URI 是应用程序的 URL 加上路径 */.auth/login/facebook/callback*。 例如，`https://contoso.azurewebsites.net/.auth/login/facebook/callback`。 请务必使用 HTTPS 方案。
   > 
   > 
7. 在左侧导航栏中，单击“设置” > “基本”。 在“应用密钥”字段中，单击“显示”，在收到请求时提供密码，并记下“应用程序 ID”和“应用密钥”的值。 稍后会在 Azure 中使用这些值来配置应用程序。
   
   > [!IMPORTANT]
   > 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
   > 
   > 
8. 用于注册应用程序的 Facebook 帐户是应用的管理员帐户。 在这种情况下，只有管理员可以登录此应用程序。 若要对其他 Facebook 帐户进行身份验证，请单击“应用审核”，并启用“使 <your-app-name> 公开”以实现能够使用 Facebook 身份验证进行常规的公开访问。

## <a name="secrets"></a>向应用程序添加 Facebook 信息
1. 返回 [Azure 门户]，导航到应用程序。 单击“设置” > “身份验证/授权”，并确保“应用服务身份验证”为“启用”。
2. 单击“Facebook”，粘贴前面获取的应用程序 ID 和应用密钥值，启用应用程序所需的任何范围（可选），并单击“确定”。
   
    ![][0]
   
    默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
3. （可选）要限制只有通过 Facebook 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Facebook”。 这会要求对所有请求进行身份验证，而所有未经身份验证的请求会被重定向到 Facebook 进行身份验证。
4. 配置身份验证完成后，单击“保存”。

现在，可以在应用中使用 Facebook 进行身份验证了。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 门户]: https://portal.azure.com/
