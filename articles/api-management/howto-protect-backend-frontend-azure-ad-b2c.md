---
title: 使用 Azure Active Directory B2C 和 Azure API 管理，通过 OAuth 2.0 保护 SPA 后端。
description: 使用 Azure Active Directory B2C、Azure API 管理和轻松身份验证从 JavaScript SPA 中调用，使用 OAuth 2.0 保护 API。
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: fde48d63bd343fbed1f82e60819131ffb043a795
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967632"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>通过 OAuth 2.0、Azure Active Directory B2C 和 Azure API 管理保护 SPA 后端

此方案说明如何配置 Azure API 管理实例以保护 API。
我们会将 OAuth 2.0 协议 Azure AD B2C 与 API 管理一起使用，以使用 EasyAuth 保护 Azure Functions 后端。

## <a name="aims"></a>旨在
我们将了解如何通过 Azure Functions 和 Azure AD B2C 简化方案来使用 API 管理。 你将创建一个调用 API 的 JavaScript （JS）应用，该应用使用 Azure AD B2C 登录用户。 然后，你将使用 API 管理的验证-jwt 策略功能来保护后端 API。

为深入了解，我们使用 EasyAuth 在后端 API 内部再次验证该令牌。

## <a name="prerequisites"></a>必备条件
若要执行本文中的步骤，必须提供：
* Azure （StorageV2）常规用途 V2 存储帐户来托管前端 JS 单页面应用
* Azure API 管理实例 
* 一个空的 Azure Function app （在 Windows 消耗计划中运行 V2 .NET Core 运行时）来托管所调用的 API
* 链接到订阅的 Azure AD B2C 租户 

尽管在实际情况下，你会在生产工作负荷中使用同一区域中的资源，但本操作指南文章部署的区域并不重要。

## <a name="overview"></a>概述
下面是在此过程完成后，所使用的组件以及这些组件之间的流。
![正在使用的组件和流](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "正在使用的组件和流")

下面是简要的步骤概述：

1. 使用范围创建 Azure AD B2C 调用（前端、API 管理）和 API 应用程序，并授予 API 访问权限
1. 创建注册或登录策略，以允许用户登录 Azure AD B2C 
1. 使用新的 Azure AD B2C 客户端 Id 和密钥配置 API 管理，以在开发人员控制台中启用 OAuth2 用户授权
1. 生成函数 API
1. 将函数 API 配置为使用新的 Azure AD B2C 客户端 ID 和密钥启用 EasyAuth 并锁定到 APIM VIP 
1. 在 API 管理中构建 API 定义
1. 为 API 管理 API 配置设置 Oauth2
1. 设置**CORS**策略并添加**validate-jwt**策略来验证每个传入请求的 OAuth 令牌
1. 生成调用应用程序以使用 API
1. 上载 JS SPA 示例
1. 用新的 Azure AD B2C 客户端 ID 和密钥配置示例 JS 客户端应用 
1. 测试客户端应用程序

## <a name="configure-azure-ad-b2c"></a>配置 Azure AD B2C 
在门户中打开 "Azure AD B2C" 边栏选项卡，然后执行以下步骤。
1. 选择 "**应用程序**" 选项卡 
1. 单击 "添加" 按钮，为以下目的创建三个应用程序
   * 前端客户端。
   * 后端函数 API。
   * 可有可无API 管理开发人员门户（除非你在消费层中运行 Azure API 管理，稍后将对此方案进行详细介绍）。
1. 为所有3个应用程序设置 WebApp/Web API，并将 "允许隐式流" 设置为 "是"，只针对前端客户端。
1. 现在，设置 "应用 ID URI"，选择唯一的内容，并与正在创建的服务相关。
1. 使用回复 url 的占位符（如 https://localhost），稍后将更新这些 url。
1. 单击 "创建"，然后为上述三个应用重复步骤2-5，记录 AppID URI、名称和应用程序 ID，以便以后用于所有三个应用。
1. 从应用程序列表中打开 API 管理开发人员门户应用程序，选择 "*密钥*" 选项卡（在 "常规" 下），然后单击 "生成密钥" 以生成身份验证密钥
1. 单击 "保存" 后，在 "安全" 位置记录密钥以便以后使用-请注意，此位置只是查看和复制此密钥的机会。
1. 现在，选择 "*已发布作用域*" 选项卡（在 "API 访问" 下）
1. 创建和命名函数 API 的范围并记录范围并填充 "完整范围" 值，然后单击 "保存"。
   > [!NOTE]
   > Azure AD B2C 作用域是你的 API 中的有效权限，其他应用程序可以从其应用程序通过 API 访问边栏选项卡请求对其进行访问，因此，你只需为所调用的 API 创建应用程序权限。
1. 打开其他两个应用程序，然后在 " *API 访问*" 选项卡下查看。
1. 向他们授予对后端 API 作用域的访问权限和已有的默认值（"访问用户的配置文件"）。
1. 通过选择 "常规" 下的 "*密钥*" 选项卡生成一个密钥选项卡，以生成身份验证密钥，并将这些密钥记录到以后的安全位置。

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>创建 "注册或登录" 用户流
1. 返回到 Azure AD B2C 边栏选项卡的根（或 "概述"） 
1. 然后选择 "用户流（策略）"，然后单击 "新建用户流"
1. 选择 "注册并登录" 用户流类型
1. 为策略指定一个名称，并将其记录在以后。
1. 然后在 "标识提供者" 下，选中 "用户 ID 注册" （这可能表示 "电子邮件注册"），然后单击 "确定"。 
1. 在 "用户属性和声明" 下，单击 "显示更多 ..."然后选择希望用户输入并在令牌中返回的声明选项。 至少选中 "显示名称" 和 "电子邮件地址"，然后单击 "确定"，然后单击 "创建"。
1. 选择在列表中创建的策略，然后单击 "运行用户流" 按钮。
1. 此操作将打开 "运行用户流" 边栏选项卡，选择 "前端" 应用程序，然后记录 "选择域" 下拉列表下显示的 b2clogin.com 域的地址。
1. 单击顶部的链接以打开 "知名的 openid 配置终结点"，并记录 authorization_endpoint 和 token_endpoint 值以及链接本身的值作为已知的 openid 配置终结点。

   > [!NOTE]
   > B2C 策略允许你公开 Azure AD B2C 登录终结点，以便能够捕获不同的数据组件，并以不同的方式登录用户。 在这种情况下，我们配置了 "注册或登录" 终结点，该终结点公开了众所周知的配置终结点，特别是在 URL 中通过 p = 参数确定了创建的策略。
   > 
   > 完成此操作后，你现在就有了一个功能业务到使用者标识平台，它将用户登录到多个应用程序。 
   > 如果你想要在此处单击 "运行用户流" 按钮（完成注册或登录过程）并了解它将采取的措施，但最终的重定向步骤会失败，因为该应用尚未部署。

## <a name="build-the-function-api"></a>生成函数 API
1. 切换回 Azure 门户中的标准 Azure AD 租户，以便可以重新配置订阅中的项 
1. 中转到 Azure 门户的 "函数应用" 边栏选项卡，打开空函数应用，并通过快速入门创建新的门户网站 "Webhook + API" 函数。
1. 将下面的示例代码粘贴到 run.csx 中的现有代码上。

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > 刚粘贴的 c # 脚本函数代码只需将行记录到函数日志中，并返回文本 "Hello World"，其中包含一些动态数据（日期和时间）。

3. 选择左侧边栏选项卡中的 "集成"，然后选择窗格右上角的 "高级编辑器"。
4. 将下面的示例代码粘贴到现有 json 上。

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. 切换回 "HttpTrigger1" 选项卡，单击 "获取函数 URL"，然后复制显示的 URL。

   > [!NOTE]
   > 刚创建的绑定只是告诉函数对刚刚复制的 URL 的匿名 http GET 请求做出响应。 （ https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) 现在可以使用可缩放的无服务器 https API，它能够返回非常简单的负载。
   > 你现在可以使用上述 URL 测试从 web 浏览器调用此 API，也可以去除 URL 的？ code = secret 部分，并证明 Azure Functions 将返回401错误。

## <a name="configure-and-secure-the-function-api"></a>配置和保护函数 API
1. 需要配置函数应用中的两个额外区域（身份验证和网络限制）。
1. 首先，让我们配置身份验证/授权，然后单击 function app 的名称（&lt;Z&gt; 函数图标旁边）以显示 "概述" 页。
1. 接下来，选择 "平台功能" 选项卡，然后选择 "身份验证/授权"。
1. 启用 "应用服务身份验证" 功能。
1. 在 "身份验证提供程序" 下选择 "Azure Active Directory"，然后从管理模式开关中选择 "高级"。
1. 将后端函数 API 的应用程序 ID （从 Azure AD B2C 粘贴到 "客户端 ID" 框中）
1. 将众所周知的开放 id 配置终结点从 "注册" 或 "登录" 策略粘贴到 "颁发者 URL" 框中（前面已记录此配置）。
1. 选择“确定”。
1. 将 "请求未经过身份验证时执行操作" 下拉列表设置为 "使用 Azure Active Directory 登录" 下拉列表，然后单击 "保存"。

   > [!NOTE]
   > 现在，如果未提供正确的密钥，则会部署函数 API 并应引发401响应，并应在出现有效请求时返回数据。
   > 通过配置 "使用 Azure AD 登录" 选项来处理未经身份验证的请求，在 EasyAuth 中添加了其他深层防御安全性。 请注意，这会在后端 Function App 和前端 SPA 之间更改未经授权的请求行为，因为 EasyAuth 将发出302重定向到 AAD，而不是未授权401的响应，因此我们将在稍后使用 API 管理来更正此问题。
   > 我们仍未应用 IP 安全，如果你拥有有效的密钥和 OAuth2 令牌，则任何人都可以从任何位置调用它-理想情况下，我们希望强制所有请求都通过 API 管理。
   > 如果你使用的是 API 管理消耗层，则你将无法执行此锁定，因为对于该层没有专用的静态 IP，你将需要依赖通过共享机密功能密钥锁定 API 调用的方法，因此不可能执行步骤11-13。

1. 关闭 "身份验证/授权" 边栏选项卡 
1. 选择 "网络"，然后选择 "访问限制"
1. 接下来，将允许的函数应用 Ip 锁定到 API 管理实例 VIP。 此 VIP 显示在门户的 "API 管理-概述" 部分中。
1. 如果要继续与函数门户交互，并执行以下可选步骤，还应在此处添加自己的公共 IP 地址或 CIDR 范围。
1. 列表中有允许条目后，Azure 会添加隐式拒绝规则以阻止所有其他地址。 

需要将 CIDR 格式的地址块添加到 "IP 限制" 面板。 如果需要添加单个地址（如 API 管理 VIP），则需要将其添加为 xx. xx. xx。

   > [!NOTE]
   > 现在，不能从任何位置通过 API 管理或地址调用函数 API。
   
## <a name="import-the-function-app-definition"></a>导入函数应用定义
1. 打开 " *API 管理" 边栏选项卡*，然后打开*实例*。
1. 从实例的 "API 管理" 部分中选择 "Api" 边栏选项卡。
1. 从 "添加新 API" 窗格中选择 "Function App"，然后从弹出项顶部选择 "完全"。
1. 单击 "浏览"，选择要在其中托管 API 的函数应用，然后单击 "选择"。
1. 为 API 提供一个名称和描述，以便 API 管理的内部使用，并将其添加到 "无限制" 的产品。
1. 请确保记录基本 URL 以供以后使用，然后单击 "创建"。

## <a name="configure-oauth2-for-api-management"></a>为 API 管理配置 Oauth2

1. 接下来，从 "安全" 选项卡中选择 Oauth 2.0 边栏选项卡，然后单击 "添加"
1. 为添加的 Oauth 终结点提供*显示名称*和*说明*的值（这些值将在下一步中显示为 Oauth2 终结点）。
1. 您可以在 "客户端注册页 URL" 中输入任何值，因为不会使用此值。
1. 检查*隐式身份验证*授予类型，并选中 "授权代码授予类型"。
1. 转到 "*授权*" 和 "*令牌*终结点" 字段，然后输入先前从众所周知的配置 xml 文档中捕获的值。
1. 向下滚动并使用 Azure AD B2C 应用程序注册中的后端函数 API 客户端 ID 填充名为 "resource" 的*其他正文参数*
1. 选择 "客户端凭据"，将 "客户端 ID" 设置为 "开发人员控制台应用的应用 ID"-如果使用消耗 API 管理模型，请跳过此步骤。
1. 将客户端密码设置为之前记录的密钥-如果使用消耗 API 管理模型，请跳过此步骤。
1. 最后，记录从 API 管理授权代码授予的 redirect_uri 以供以后使用。

## <a name="set-up-oauth2-for-your-api"></a>为 API 设置 Oauth2
1. 你的 API 将显示在门户的 "所有 Api" 部分下，通过单击它打开 API。
1. 选择 "设置" 选项卡。
1. 通过从 "用户授权" 单选按钮中选择 "Oauth 2.0" 来更新设置。
1. 选择前面定义的 Oauth 服务器。
1. 选中 "替代作用域" 复选框，并输入之前在中为后端 API 调用记录的作用域。

   > [!NOTE]
   > 现在，我们有了一个 API 管理实例，该实例知道如何从 Azure AD B2C 获取访问令牌来授权请求并了解我们的 Oauth2 Azure Active Directory B2C 配置。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>设置**CORS**和**验证-jwt**策略

> 无论使用何种 APIM 层，都应遵循以下各节。 

1. 切换回 "设计" 选项卡，选择 "所有 Api"，然后单击代码视图按钮以显示策略编辑器。
1. 编辑入站部分并粘贴以下 xml，使其类似于以下内容。

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. 编辑 openid-config url，使其与注册或登录策略的已知 Azure AD B2C 终结点匹配。
1. 编辑声明值以匹配有效的应用程序 ID （也称为后端 API 应用程序的客户端 ID）并保存。

   > [!NOTE]
   > 现在，API 管理可以响应对 JS SPA 应用程序的跨源请求，并将在将请求转发到函数 API 之前，对要传递的 JWT 身份验证令牌执行限制、速率限制和预验证。

   > [!NOTE]
   > 以下部分是可选的，不适用于不支持开发人员门户的**消耗**层。
   > 如果你不打算使用开发人员门户，或者不能使用它，因为你使用的是消耗层，请跳过此步骤并直接跳到["构建 JAVASCRIPT SPA 以使用 API"](##build-the-javascript-spa-to-consume-the-api)。

## <a name="optional-configure-the-developer-portal"></a>可有可无配置开发人员门户

1. 打开 "Azure AD B2C" 边栏选项卡，然后导航到开发人员门户的 "应用程序注册"
1. 将 "回复 URL" 条目设置为之前在 API 管理中配置身份验证代码授予的 redirect_uri 时记下的条目。

   既然已在 `Echo API`上启用 OAuth 2.0 用户授权，开发人员控制台在调用 API 之前，会为用户获取访问令牌。

1. 在开发人员门户中，浏览到 `Echo API` 下的任一操作，并选择 "**试用**" 以使你转到开发人员控制台。
1. 可以看到，“授权”部分出现了一个与刚刚添加的授权服务器对应的新项。
1. 从 "授权" 下拉列表中选择 "**授权代码**"，系统将提示登录到 Azure AD 租户。 如果已在帐户中登录，则可能不会出现提示。
1. 成功登录后，会将 `Authorization: Bearer` 标头添加到请求中，并将 Azure AD B2C 以 Base64 编码的访问令牌。 
1. 选择 "**发送**"，然后即可成功调用 API。

   > [!NOTE]
   > 现在，API 管理能够获取开发人员门户的令牌来测试 API，并能够理解其定义，并在开发人员门户中呈现适当的测试页。

1. 在 API 管理门户的 "概述" 边栏选项卡中，单击 "开发人员门户" 以 API 的管理员身份登录。
1. 此时，你和你的 API 的其他选定使用者可以从控制台测试并调用它们。
1. 选择 "产品"，选择 "无限制"，然后选择前面创建的 API，然后单击 "试用"
1. 取消隐藏 API 订阅密钥，并将其与稍后需要的请求 url 一起复制到其中。
1. 此外，从 "oauth 身份验证" 下拉列表中选择 "隐式"，可能需要使用弹出窗口进行身份验证。
1. 单击 "发送"，如果一切正常，你的 Function App 应通过 API 管理使用一条200良好的消息和一些 JSON 回复消息。

   > [!NOTE]
   > 恭喜，你现在已 Azure AD B2C、API 管理和 Azure Functions 协同工作，以发布、保护和使用 API。 你可能已注意到，使用此方法对 API 执行了两次保护，一次使用 API 管理 Ocp-订阅密钥标头，一次使用授权：持有者 JWT。
   > 你将是正确的，因为此示例是一个 JavaScript 单页应用程序，因此，我们只使用 API 管理密钥进行速率限制和计费呼叫。
   > 实际的授权和身份验证由 Azure AD B2C 处理，并封装在 JWT 中，通过 API 管理对此进行验证两次，一次验证，然后 Azure Functions。

## <a name="build-the-javascript-spa-to-consume-the-api"></a>构建 JavaScript SPA 以使用 API
1. 在 Azure 门户中打开 "存储帐户" 边栏选项卡 
1. 选择所创建的帐户，然后从 "设置" 部分选择 "静态网站" 边栏选项卡（如果看不到 "静态网站" 选项，请检查创建的 V2 帐户）。
1. 将静态 web 宿主功能设置为 "已启用"，将索引文档名称设置为 "index .html"，然后单击 "保存"。
1. 记下主终结点的内容，因为此位置是将承载前端站点的位置。 

   > [!NOTE]
   > 你可以使用 Azure Blob 存储 + CDN 重写或 Azure App Service-但 Blob 存储的静态网站托管功能为我们提供了一个默认容器来提供 Azure 存储中的静态 web 内容/html/js/css，并为零工作推理出默认页面。

## <a name="upload-the-js-spa-sample"></a>上载 JS SPA 示例
1. 仍在 "存储帐户" 边栏选项卡中，从 "Blob 服务" 部分选择 "Blob" 边栏选项卡，并单击右侧窗格中显示的 $web 容器。
1. 将以下代码保存到计算机上本地的文件中作为索引 html，然后将文件索引 html 上载到 $web 容器。

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. 浏览到上一部分前面存储的静态网站主终结点。

   > [!NOTE]
   > 恭喜，你刚刚将 JavaScript 单页应用部署到 Azure 存储，因为我们尚未使用你的 api 的密钥配置了 JS 应用或已使用 Azure AD B2C 的详细信息配置了 JS 应用–如果你打开该页面，此页面将不起作用。

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>将 JS SPA 配置 Azure AD B2C
1. 现在，我们知道一切都是：我们可以用合适的 API 管理 API 地址和正确的 Azure AD B2C 应用程序/客户端 Id 配置 SPA
1. 返回 Azure 门户存储 "边栏选项卡，单击" html "，然后选择" 编辑 Blob " 
1. 更新 "身份验证详细信息"，使其与先前在 B2C 中注册的前端应用程序相匹配，请注意 "b2cScopes" 值适用于 API 后端。
1. 可在 api 管理测试窗格中找到用于 API 操作的 webApi 密钥和 api url。
1. 按标题创建 APIM 订阅密钥，并将其添加回 api 管理边栏选项卡，选择 "订阅"，然后单击 "添加订阅"，然后保存记录。 单击创建行旁边的省略号（...），可以显示密钥，以便复制主键。
1. 它应类似于以下代码：-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. 点击“保存”

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>设置 Azure AD B2C 前端应用的重定向 Uri
1. 打开 "Azure AD B2C" 边栏选项卡，然后导航到 JavaScript 前端应用程序的应用程序注册
1. 将重定向 URL 设置为你在前面设置静态网站主终结点时记下的 URL

   > [!NOTE] 
   > 此配置将导致前端应用程序的客户端使用 Azure AD B2C 中的相应声明接收访问令牌。
   > SPA 会将此作为持有者令牌添加到后端 API 调用的 https 标头中。
   > 在将请求传递到接收 Azure Function API 之前，API 管理将通过订阅者密钥预先验证令牌、速率限制对终结点的调用。
   > SPA 将在浏览器中呈现响应。

   > *恭喜，你已配置 Azure AD B2C、Azure API 管理、Azure Functions Azure App Service 授权才能完美协调！*

   > [!NOTE]
   > 现在，我们有了一个具有简单安全的 API 的简单应用程序，我们来对其进行测试。

## <a name="test-the-client-application"></a>测试客户端应用程序
1. 打开你在前面创建的存储帐户中记下的示例应用 URL
1. 单击右上角的 "登录"，单击此项将弹出 Azure AD B2C 注册或登录配置文件。
1. 屏幕上的 "登录身份" 部分中的 Post 登录将从你的 JWT 填充。
1. 现在，请单击 "调用 Web Api"，此时应会更新页面，使其值从安全的 API 发回。

## <a name="and-were-done"></a>我们已经完成了
可以改编和编辑上述步骤，以便在 API 管理中使用 Azure AD B2C 的多种不同用途。

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-scenarios.md)。
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。
* 请考虑使用 Azure AD 图形 API 来分配自定义声明，并使用 API 管理策略来验证令牌中是否存在这些声明。

* [创建 API 管理服务实例](get-started-create-service-instance.md)。

* [管理第一个 API](import-and-publish.md)。
