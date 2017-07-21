---
title: "使用移动应用在 Apache Cordova 中添加身份验证 | Microsoft Docs"
description: "了解如何使用 Azure 应用服务中的移动应用通过各种标识提供者（包括 Google、Facebook、Twitter 和 Microsoft）对 Apache Cordova 应用用户进行身份验证。"
services: app-service\mobile
documentationcenter: javascript
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: f2073819fe60aa51b88feeaf3b0ff0e8d052b4c7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="add-authentication-to-your-apache-cordova-app"></a>将身份验证添加到 Apache Cordova 应用
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>摘要
本教程介绍如何使用支持的标识提供者将身份验证添加到 Apache Cordova 上的待办事项列表快速入门项目。 本教程基于[移动应用入门]教程，必须先完成该教程。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

现在，可以验证是否已禁用对后端的匿名访问。 在 Visual Studio 中：

* 打开完成[移动应用入门]教程后创建的项目。
* 在“Google Android 模拟器”中运行应用程序。
* 验证在应用启动后，是否显示“意外的连接失败”。

接下来，需要更新应用程序，以便在从移动应用后端请求资源之前对用户进行身份验证。

## <a name="add-authentication"></a>向应用程序添加身份验证
1. 在 **Visual Studio** 中打开项目，然后打开 `www/index.html` 文件进行编辑。
2. 找到 head 节中的 `Content-Security-Policy` 元标记。  将 OAuth 主机添加到允许的源列表。

   | 提供程序 | SDK 提供程序名称 | OAuth 主机 |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | facebook | https://www.facebook.com |
   | Google | google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | twitter | https://api.twitter.com |

    下面显示了 Content-Security-Policy（针对 Azure Active Directory 实现）的示例：

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    将 `https://login.microsoftonline.com` 替换为上表中的 OAuth 主机。  有关 content-security-policy 元标记的详细信息，请参阅 [Content-Security-Policy 文档]。

    在相应的移动设备上使用时，某些身份验证提供程序不需要 Content-Security-Policy 更改。  例如，在 Android 设备上使用 Google 身份验证时便不需要 Content-Security-Policy 更改。

3. 打开 `www/js/index.js` 文件进行编辑，找到 `onDeviceReady()` 方法，然后在客户端创建代码下面添加以下代码：

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
[Content-Security-Policy 文档]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[推送通知]: app-service-mobile-cordova-get-started-push.md
[关于身份验证]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

