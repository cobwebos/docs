---
title: Azure AD AngularJS 入门 | Microsoft Docs
description: 如何生成一个与 Azure AD 集成以方便登录，并使用 OAuth 调用 Azure AD 保护 API 的 AngularJS 单页面应用程序。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c7f6a0e447e3b48cdd1df684dc105ece1e98f66
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39580053"
---
# <a name="azure-ad-angularjs-getting-started"></a>Azure AD AngularJS 入门

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

使用 Azure Active Directory (Azure AD) 可以简单直接地在单页面应用程序中添加登录、注销和安全 OAuth API 调用。 它允许应用使用用户的 Windows Server Active Directory 帐户对用户进行身份验证，并使用 Azure AD 帮助保护的任何 Web API（例如 Office 365 API 或 Azure API）。

对于浏览器中运行的 javascript 应用程序，Azure AD 提供 Active Directory 身份验证库 (ADAL)，即 adal.js。 adal.js 的唯一用途就是方便应用获取访问令牌。 为了演示这种简便性，我们生成了一个 AngularJS 待办事项列表应用程序，其中包括：

* 使用 Azure AD 作为标识提供程序将用户登录到应用。

* 显示有关用户的一些信息。
* 使用 Azure AD 提供的持有者令牌安全调用应用的待办事项列表 API。
* 从应用程序中注销用户。

若要生成完整的工作应用程序，需要：

1. 将应用注册到 Azure AD。
2. 安装 ADAL 并配置单页面应用。
3. 使用 ADAL 帮助保护单页面应用中的页面。

若要开始，请[下载应用框架](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。 还需要一个可在其中创建用户和注册应用程序的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](quickstart-create-new-tenant.md)。

## <a name="step-1-register-the-directorysearcher-application"></a>步骤 1：注册 DirectorySearcher 应用程序
若要使应对用户进行身份验证并获取令牌，首先需要在 Azure AD 租户中注册该应用：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 如果登录到多个目录，可能需要确保正在查看正确目录。 若要执行此操作，在顶部栏上，单击你的帐户。 在“目录”列表下选择要注册应用程序的 Azure AD 租户。
3. 在左窗格中，单击“所有服务”，并选择“Azure Active Directory”。
4. 单击“应用注册”，并选择“添加”。
5. 根据提示创建一个新的 Web 应用程序和/或 Web API：
  * **名称**向用户描述应用程序。
  * “登录 URL”是 Azure AD 要将令牌返回到的位置。 本示例的默认位置是 `https://localhost:44326/`。
6. 完成注册后，Azure AD 将向应用分配唯一应用程序 ID。 在后面的部分中会用到此值，因此，请从应用程序选项卡中复制此值。
7. Adal.js 使用 OAuth 隐式流来与 Azure AD 通信。 必须为应用程序启用隐式流：
  1. 单击应用程序，并选择“清单”打开内联清单编辑器。
  2. 找到 `oauth2AllowImplicitFlow` 属性。 将其值设置为 `true`。
  3. 单击“保存”以保存清单。
8. 针对应用程序，在租户中授予权限。 转到“设置” > “所需的权限”，并单击顶部栏上的“授予权限”按钮。 单击“是”确认。

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>步骤 2：安装 ADAL 并配置单页面应用
将应用程序注册到 Azure AD 后，可以安装 adal.js 并编写标识相关的代码。

### <a name="configure-the-javascript-client"></a>配置 JavaScript 客户端
首先，使用程序包管理器控制台将 adal.js 添加到 TodoSPA 项目：
  1. 下载 [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) 并将其添加到 `App/Scripts/` 项目目录。
  2. 下载 [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) 并将其添加到 `App/Scripts/` 项目目录。
  3. 在 `index.html` 将每个脚本加载到 `</body>` 的末尾之前：

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>配置后端服务器
要使单页面应用的后端待办事项列表 API 接受来自浏览器的令牌，后端需要有关应用注册的配置信息。 在 TodoSPA 项目中，打开 `web.config`。 替换 `<appSettings>` 部分中的元素值，反映在 Azure 门户中使用的值。 只要使用 ADAL，代码就会引用这些值。
  * `ida:Tenant` 是 Azure AD 租户的域，例如 contoso.onmicrosoft.com。
  * `ida:Audience` 是从门户复制的应用程序的客户端 ID。

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>步骤 3：使用 ADAL 帮助保护单页面应用中的页面
Adal.js 与 AngularJS 路由和 HTTP 提供程序集成，以便可以帮助保护单页面应用中的各个视图。

1. 在 `App/Scripts/app.js` 中引入 adal.js 模块：

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. 使用应用程序注册的配置值初始化 `adalProvider`（同样在 `App/Scripts/app.js` 中）：

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. 只需使用一行代码，即可帮助保护应用中的 `TodoList` 视图：`requireADLogin`。

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>摘要
现在，已获得一个安全的单页面应用，它可以让用户登录，并可向其后端 API 发出受持有者令牌保护的请求。 用户单击 **TodoList** 链接时，adal.js 会根据需要自动重定向到 Azure AD 以进行登录。 此外，adal.js 会自动将访问令牌附加到已发送至应用后端的任何 Ajax 请求。 

前面的步骤仅是使用 adal.js 生成单页面应用的最低必要条件。 但一些其他功能在单页面应用中很有用：

* 若要显式发出登录和注销请求，可以在调用 adal.js 的控制器中定义函数。 在 `App/Scripts/homeCtrl.js` 中：

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* 可能需要在应用的 UI 中显示用户信息。 ADAL 服务已添加到 `userDataCtrl` 控制器，因此可在关联的视图 `App/Views/UserData.html` 中访问 `userInfo` 对象：

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* 在许多情况下，你希望知道用户是否已登录。 也可以使用 `userInfo` 对象来收集此信息。 例如，在 `index.html` 中，可以根据身份验证状态显示“登录”或“注销”按钮：

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

集成了 Azure AD 的单页面应用可对用户进行身份验证，使用 OAuth 2.0 安全调用其后端，并获取有关用户的基本信息。 如果尚未这样做，可以在租户中填充一些用户。 运行待办事项列表单页面应用，并使用这些用户之一进行登录。 将任务添加到用户的待办事项列表、注销并重新登录。

使用 Adal.js 可以方便地将常见标识功能合并到应用程序中。 它负责所有的繁琐工作：缓存管理、OAuth 协议支持、向用户显示登录 UI、刷新已过期的令牌等。

[GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) 中提供了已完成示例（无配置值）以供参考。

## <a name="next-steps"></a>后续步骤
现在，可以转到其他方案。 可能需要：[从单页面应用调用 CORS Web API](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
