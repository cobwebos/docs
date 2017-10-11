---
title: "Azure AD AngularJS 入门 |Microsoft 文档"
description: "如何生成的 AngularJS 单页面应用程序与 Azure AD 集成以登录和使用 OAuth 调用 Azure AD 保护 Api。"
services: active-directory
documentationcenter: 
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4153910bc03f112f84c26cda6f9c78f11028b934
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>帮助通过使用 Azure AD 保护 AngularJS 单页面应用程序

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) 使其简单明了使你可以添加在登录、 注销，和安全 OAuth API 调用向你的单页面应用。  它可让你的应用以使用其 Windows Server Active Directory 帐户的用户进行身份验证和使用任何 web API，Azure AD 可帮助保护，例如 Office 365 Api 或 Azure API。

对于浏览器中运行的 JavaScript 应用程序，Azure AD 提供 Active Directory 身份验证库 (ADAL)，即 adal.js。 Adal.js 的唯一目的是方便应用程序获取访问令牌。 为了演示操作的简单性，下面我们要生成 AngularJS 待办事项列表应用程序的：

* 用户在登录到应用程序通过使用 Azure AD 作为标识提供程序。

* 显示有关用户的一些信息。
* 通过使用 Azure AD 的持有者令牌安全地调用应用程序的到待办事项 API。
* 将用户从应用进行签名。

若要生成完成后，工作应用程序，你需要：

1. 向 Azure AD 注册您的应用程序。
2. 安装 ADAL 并配置单页面应用程序。
3. 使用 ADAL 来帮助在单页面应用程序中的安全页。

若要开始，[下载应用程序框架](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)或[下载已完成的示例](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。 你还需要一个 Azure AD 租户，你可在其中创建用户和注册应用程序。 如果你还没有租户，[了解如何获取租户](active-directory-howto-tenant.md)。

## <a name="step-1-register-the-directorysearcher-application"></a>步骤 1： 注册 DirectorySearcher 应用程序
若要启用您的应用程序用户进行身份验证并获取令牌，首先需要在你的 Azure AD 租户中注册：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 如果你登录到多个目录，你可能需要确保你正在查看正确的目录。 为此，顶部栏上，单击你的帐户。 下**目录**列表中，选择想要注册你的应用程序的 Azure AD 租户。
3. 单击**更服务**在左窗格中，，然后选择**Azure Active Directory**。
4. 单击**应用程序注册**，然后选择**添加**。
5. 按照提示进行操作，然后创建一个新的 web 应用程序和/或 web API:
  * **名称**描述用户应用程序。
  * **重定向 Uri**是 Azure AD 将令牌返回到的位置。 此示例的默认位置是`https://localhost:44326/`。
6. 在完成注册后，Azure AD 将向应用程序分配唯一的应用程序 ID。  你将需要此值在下一步的部分中，因此它将从复制应用程序选项卡。
7. Adal.js 使用 OAuth 隐式流来与 Azure AD 通信。 你必须对应用程序启用隐式流：
  1. 单击应用程序，然后选择**清单**打开内联清单编辑器。
  2. 找到`oauth2AllowImplicitFlow`属性。 将其值设置为`true`。
  3. 单击**保存**保存清单。
8. 在你为应用程序的租户中授予权限。 转到**设置** > **属性** > **所需的权限**，然后单击**Grant 权限**顶部栏上的按钮。 单击“是”  以确认。

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>步骤 2： 安装 ADAL 并配置单页面应用
现在，你有 Azure AD 中的应用程序，可以安装 adal.js 并编写标识相关的代码。

### <a name="configure-the-javascript-client"></a>配置 JavaScript 客户端
开始通过使用 Package Manager Console 将 adal.js 添加到 TodoSPA 项目：
  1. 下载[adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js)和将其添加到`App/Scripts/`项目目录。
  2. 下载[adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js)和将其添加到`App/Scripts/`项目目录。
  3. 结束之前每个脚本加载`</body>`中`index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>将后端服务器配置
对于单页面应用程序的后端到执行列表 API 接受从浏览器的令牌后, 端需要有关应用程序注册的配置信息。 在 TodoSPA 项目中，打开`web.config`。 中的元素的值替换`<appSettings>`部分以反映你在 Azure 门户中使用的值。 只要使用 ADAL，你的代码将引用这些值。
  * `ida:Tenant`是 Azure AD 租户-例如，contoso.onmicrosoft.com 的域。
  * `ida:Audience`是你从门户复制的应用程序的客户端 ID。

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>步骤 3： 使用 ADAL 以帮助单页面应用程序中的安全页
Adal.js 与 AngularJS 路由和 HTTP 提供程序集成，因此单页面应用程序中有助于安全的单个视图。

1. 在`App/Scripts/app.js`，引入 adal.js 模块：

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. 初始化`adalProvider`中还使用你的应用程序注册的配置值`App/Scripts/app.js`:

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
3. 帮助保护`TodoList`视图在应用程序中使用只有一个代码行： `requireADLogin`。

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>“摘要”
你现在具有的安全的单页面应用程序可以在用户登录并向其后端 API 发出持有者令牌保护请求。 当用户单击**TodoList**链接，adal.js 会自动重定向到 Azure AD 进行登录如有必要。 此外，adal.js 将会自动将访问令牌附加到应用程序的后端发送的任何 Ajax 请求。  

前面的步骤是裸机的最小必要时，通过使用 adal.js 生成单页面应用程序。 但其他一些功能会单页面应用程序中很有用：

* 若要显式发出登录和注销请求，可以在你调用 adal.js 的控制器中定义函数。  In `App/Scripts/homeCtrl.js`:

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
* 你可能想要在应用程序的用户界面中显示的用户信息。 ADAL 服务已添加到`userDataCtrl`控制器，以便可以访问`userInfo`中关联的视图中，对象`App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* 有很多情况下，你将需要知道如果用户或不登录。 你还可以使用`userInfo`对象来收集此信息。  例如，在`index.html`，你可以显示任一**登录**或**注销**按钮基于身份验证状态：

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure AD 集成的单页应用程序可以对用户进行身份验证，安全地使用 OAuth 2.0 中，调用其后端并获取有关用户的基本信息。 如果你尚未这样做，现在是与某些用户在租户中填充的时间。 运行待办事项列表单页面应用程序，并使用这些用户之一进行登录。 将任务添加到用户的待办事项列表、 注销，然后重新登录。

使用 Adal.js 可以方便地将常见标识功能合并你的应用程序。 它会为你负责所有的繁琐工作： 缓存管理、 OAuth 协议支持、 向用户显示登录的 UI，这刷新过期的令牌，等等。

对于引用，已完成的示例 （无需你的配置值） 可用于[GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。

## <a name="next-steps"></a>后续步骤
现在，你可以转到其他方案。 你可能想要尝试：[从单页面应用中调用 CORS web API](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
