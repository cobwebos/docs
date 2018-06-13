---
title: 如何为应用服务应用程序配置 Microsoft 帐户身份验证
description: 了解如何为应用服务应用程序配置 Microsoft 帐户身份验证。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 4fb5bdf30502dbca3eba961165a1ab643427abd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149997"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>如何将应用服务应用程序配置为使用 Microsoft 帐户登录
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题说明如何将 Azure 应用服务配置为使用 Microsoft 帐户作为身份验证提供程序。 

## <a name="register-microsoft-account"> </a>使用 Microsoft 帐户注册应用
1. 登录到 [Azure 门户]并导航到应用程序。 复制 **URL**，随后会用于使用 Microsoft 帐户来配置应用。
2. 在 Microsoft 帐户开发人员中心内导航到[我的应用程序]页，并根据需要使用 Microsoft 帐户登录。
3. 单击“添加应用”，键入应用程序名称，并单击“创建”。
4. 记下“应用程序 ID”，因为稍后将要用到。 
5. 在“平台”下，单击“添加平台”，并选择“Web”。
6. 在“重定向 URI”下，提供应用程序的终结点，并单击“保存”。 
   
   > [!NOTE]
   > 重定向 URI 是应用程序 URL 加上路径 /.auth/login/microsoftaccount/callback。 例如，`https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`。   
   > 请务必使用 HTTPS 方案。
   
7. 在“应用程序机密”下，单击“生成新密码”。 请记下显示的值。 关闭页面后，就不再显示该值。

    > [!IMPORTANT]
    > 密码是一个非常重要的安全凭据。 请不要与任何人共享密码或者在客户端应用程序中分发它。

## <a name="secrets"> </a>向应用服务应用程序添加 Microsoft 帐户信息
1. 返回 [Azure 门户]，导航到应用程序，并单击“设置” > “身份验证/授权”。
2. 如果“身份验证/授权”功能未启用，请将它切换为“打开”。
3. 单击“Microsoft 帐户”。 粘贴前面获取的应用程序 ID 和密码值，启用应用程序所需的任何范围（可选）。 然后单击“确定”。
   
    ![][1]
   
    默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
4. （可选）要限制只有通过 Microsoft 帐户身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“Microsoft 帐户”。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Microsoft 帐户进行身份验证。
5. 单击“ **保存**”。

现在，可以使用 Microsoft 帐户在应用中进行身份验证。

## <a name="related-content"></a>相关内容
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[我的应用程序]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 门户]: https://portal.azure.com/
