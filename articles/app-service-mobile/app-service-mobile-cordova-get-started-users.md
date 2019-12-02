---
title: Apache Cordova 上添加身份验证
description: 了解如何在 Azure App Service 中使用移动应用，通过 Google、Facebook、Twitter 和 Microsoft 等标识提供者对 Apache Cordova 应用程序的用户进行身份验证。
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f85c8e4f8eedccf3039038308f2262727fb18197
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668554"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>将身份验证添加到 Apache Cordova 应用
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”、“测试”和“分发”服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用“分析”和“诊断”服务监视其应用的状态和使用情况，并使用“推送”服务吸引用户。 开发人员还可以利用“身份验证”对其用户进行身份验证，并使用“数据”服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="summary"></a>总结
本教程介绍如何使用支持的标识提供者将身份验证添加到 Apache Cordova 上的待办事项列表快速入门项目。 本教程基于[移动应用入门]教程，必须先完成该教程。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>将权限限制为已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

现在，可以验证是否已禁用对后端的匿名访问。 在 Visual Studio 中：

* 打开完成[移动应用入门]教程后创建的项目。
* 在“Google Android 模拟器”中运行应用程序。
* 验证在应用启动后，是否显示“意外的连接失败”。

接下来，需要更新应用程序，以便在从移动应用后端请求资源之前对用户进行身份验证。

## <a name="add-authentication"></a>向应用添加身份验证
1. 在 **Visual Studio** 中打开项目，并打开 `www/index.html` 文件进行编辑。
2. 找到 head 节中的 `Content-Security-Policy` 元标记。  将 OAuth 主机添加到允许的源列表。

   | 提供商 | SDK 提供程序名称 | OAuth 主机 |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    下面显示了 Content-Security-Policy（针对 Azure Active Directory 实现）的示例：

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    将 `https://login.microsoftonline.com` 替换为上表中的 OAuth 主机。  有关 content-security-policy 元标记的详细信息，请参阅 [Content-Security-Policy]。

    在相应的移动设备上使用时，某些身份验证提供程序不需要 Content-Security-Policy 更改。  例如，在 Android 设备上使用 Google 身份验证时便不需要 Content-Security-Policy 更改。

3. 打开 `www/js/index.js` 文件进行编辑，找到 `onDeviceReady()` 方法，并在客户端创建代码下面添加以下代码：

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    此代码将替换用于创建表引用和刷新 UI 的现有代码。

    login() 方法开始对提供程序进行身份验证。 login() 方法是返回 JavaScript Promise 的异步函数。  初始化的剩余部分放置在 promise 响应中，因此在 login() 方法完成之前不会执行。

4. 在刚刚添加的代码中，将 `SDK_Provider_Name` 替换为登录提供程序的名称。 例如，对于 Azure Active Directory，请使用 `client.login('aad')`。
5. 运行项目。  当项目已完成初始化后，应用程序将针对所选的身份验证提供程序显示 OAuth 登录页。

## <a name="next-steps"></a>后续步骤
* 了解有关 Azure 应用服务中的[关于身份验证]。
* 将[推送通知]添加到 Apache Cordova 应用，继续学习本教程。

了解如何使用 SDK。

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs. -->
[移动应用入门]: app-service-mobile-cordova-get-started.md
[Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[推送通知]: app-service-mobile-cordova-get-started-push.md
[关于身份验证]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
