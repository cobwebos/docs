---
title: 配置 Facebook 身份验证 - Azure 应用服务
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
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f4d26572dc21e2c7454fb739cb4b5fb5665bd1db
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098585"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>如何将应用服务应用程序配置为使用 Facebook 登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍了如何将 Azure 应用服务配置为使用 Facebook 作为验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Facebook 帐户和一个手机号码。 若要创建新的 Facebook 帐户，请转到 [facebook.com]。

## <a name="register"> </a>向 Facebook 注册应用程序
1. 导航到[Facebook Developers]网站, 并以 facebook 帐户凭据登录。
3. 可有可无如果你没有 Facebook for 开发人员帐户, 请单击 "**入门**", 然后按照注册步骤操作。
4.  > 单击 **"我的应用" "** **添加新应用**"。
5. 在**显示名称**中，键入应用的唯一名称。 此外，还提供**联系人电子邮件**，然后单击“创建应用 ID”并完成安全检查。 这会转到新 Facebook 应用的开发人员仪表板。
6. 单击 "**仪表板** > **Facebook 登录** > **设置** > **Web**"。
1. 在 " **Facebook 登录**" 下的左侧导航栏中, 单击 "**设置**"。
1. 在 "**有效的 OAuth 重定向 uri**" 中, 键入`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`并将 *\<app-name >* 替换为 Azure App Service 应用的名称。 单击**保存更改**。
8. 在左侧导航栏中，单击“设置” > “基本”。 在 "**应用密码**" 字段中, 单击 "**显示**"。 复制 "**应用 ID** " 和 "**应用机密**" 的值。 稍后将使用这些配置来配置 Azure 中的应用服务应用。
   
   > [!IMPORTANT]
   > 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
   > 
   > 
9. 用于注册应用程序的 Facebook 帐户是应用的管理员帐户。 在这种情况下，只有管理员可以登录此应用程序。 若要对其他 Facebook 帐户进行身份验证, 请单击 "**应用审核**", 并启用 **\<** "应用程序名称 >", 以使用 Facebook 身份验证启用常规公共访问。

## <a name="secrets"></a>向应用程序添加 Facebook 信息
1. 登录到[Azure 门户]并导航到应用服务应用。 单击“设置” > “身份验证/授权”，并确保“应用服务身份验证”为“启用”。
2. 单击“Facebook”，粘贴前面获取的应用程序 ID 和应用密钥值，启用应用程序所需的任何范围（可选），并单击“确定”。
   
    ![][0]
   
    默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
3. （可选）要限制只有通过 Facebook 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Facebook”。 这会要求对所有请求进行身份验证，而所有未经身份验证的请求会被重定向到 Facebook 进行身份验证。
 
> [!CAUTION]
> 以这种方式限制访问权限适用于对应用的所有调用, 对于需要公开提供的主页的应用, 与在许多单页应用程序中一样。 对于此类应用程序, 可以首选 "**允许匿名请求 (无操作)** ", 应用手动启动登录, 如[此处](overview-authentication-authorization.md#authentication-flow)所述。

4. 配置身份验证完成后，单击“保存”。

现在，可以在应用中使用 Facebook 进行身份验证了。

## <a name="related-content"> </a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 门户]: https://portal.azure.com/
