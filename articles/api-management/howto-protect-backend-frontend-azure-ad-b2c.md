---
title: 通过使用 Azure 活动目录 B2C 和 Azure API 管理，使用 OAuth 2.0 保护 SPA 后端。
description: 通过使用 Azure 活动目录 B2C、Azure API 管理和从 JavaScript SPA 调用的简易 Auth，使用 OAuth 2.0 保护 API。
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
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475470"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>使用 OAuth 2.0、Azure 活动目录 B2C 和 Azure API 管理保护 SPA 后端

本方案演示如何配置 Azure API 管理实例以保护 API。
我们将使用 OAuth 2.0 协议与 Azure AD B2C 以及 API 管理一起使用，使用 EasyAuth 保护 Azure 函数后端。

## <a name="aims"></a>目标
我们将了解如何在 Azure 函数和 Azure AD B2C 的简化方案中使用 API 管理。 您将创建一个调用 API 的 JavaScript （JS） 应用，该应用使用 Azure AD B2C 在用户中签名。 然后，您将使用 API 管理的验证 jwt 策略功能来保护后端 API。

为了深入防御，我们使用 EasyAuth 在后端 API 内再次验证令牌。

## <a name="prerequisites"></a>先决条件
若要执行本文中的步骤，必须提供：
* 用于托管前端 JS 单页应用的 Azure（存储V2）通用 V2 存储帐户
* Azure API 管理实例 
* 空 Azure 函数应用（在 Windows 消耗计划中运行 V2 .NET 核心运行时），以承载被调用的 API
* 链接到订阅的 Azure AD B2C 租户 

尽管实际上您将在同一区域中使用生产工作负载中的资源，但对于此操作操作文章，部署区域并不重要。

## <a name="overview"></a>概述
下面是在此过程完成后使用的组件及其之间流动的说明。
![正在使用的组件和流](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "正在使用的组件和流")

下面是简要的步骤概述：

1. 使用作用域创建 Azure AD B2C 调用（前端、API 管理）和 API 应用程序并授予 API 访问权限
1. 创建注册或登录策略，以允许用户使用 Azure AD B2C 登录 
1. 使用新的 Azure AD B2C 客户端标识和密钥配置 API 管理，以便在开发人员控制台中启用 OAuth2 用户授权
1. 构建函数 API
1. 配置功能 API 以使用新的 Azure AD B2C 客户端 ID 和密钥启用 EasyAuth，并锁定到 APIM VIP 
1. 在 API 管理中构建 API 定义
1. 为 API 管理 API 配置设置 Oauth2
1. 设置**CORS**策略并添加**验证 jwt**策略，以验证每个传入请求的 OAuth 令牌
1. 构建调用应用程序以使用 API
1. 上传 JS SPA 示例
1. 使用新的 Azure AD B2C 客户端 ID 和密钥配置示例 JS 客户端应用 
1. 测试客户端应用程序

## <a name="configure-azure-ad-b2c"></a>配置 Azure AD B2C 
在门户中打开 Azure AD B2C 边栏选项卡，然后执行以下步骤。
1. 选择 **"应用程序**"选项卡 
1. 单击"添加"按钮并创建三个应用程序，用于以下目的
   * 前端客户端。
   * 后端函数 API。
   * [可选]API 管理开发人员门户（除非您在消费层中运行 Azure API 管理，稍后将介绍此方案的更多内容）。
1. 为所有 3 个应用程序设置 WebApp / Web API，并将"允许隐式流"设置为"仅为前端客户端"。
1. 现在设置应用 ID URI，选择与正在创建的服务相关的唯一内容。
1. 现在对答复 URL 使用占位符，例如https://localhost，我们将在稍后更新这些 URL。
1. 单击"创建"，然后对上述三个应用中的每一个重复步骤 2-5，记录 AppID URI、名称和应用程序 ID，以便以后用于所有三个应用。
1. 从应用程序列表中打开 API 管理开发人员门户应用程序，然后选择 *"常规"* 选项卡（在"常规"下），然后单击"生成密钥"以生成 auth 键
1. 单击"保存"后，将密钥记录在安全的地方以供以后使用 - 请注意，这个地方是您查看和复制此密钥的唯一机会。
1. 现在选择*已发布的作用域*选项卡（在 API 访问下）
1. 为函数 API 创建和命名作用域并记录范围和填充的完整范围值，然后单击"保存"。
   > [!NOTE]
   > Azure AD B2C 作用域实际上是 API 中的权限，其他应用程序可以通过 API 访问边栏选项卡请求访问其应用程序的权限，实际上您刚刚为所谓的 API 创建了应用程序权限。
1. 打开其他两个应用程序，然后在 API*访问*选项卡下查看。
1. 授予他们对后端 API 作用域和已存在的默认 API 作用域（"访问用户配置文件"）的访问权限。
1. 通过选择"常规"下的 *"键*"选项卡来生成每个键，以生成 auth 键，并将这些密钥记录在安全的地方，以便以后使用。

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>创建"注册或登录"用户流
1. 返回到 Azure AD B2C 刀片的根（或"概述"） 
1. 然后选择"用户流（策略）"，然后单击"新用户流"
1. 选择"注册并登录"用户流类型
1. 为策略指定一个名称，并记录它供以后使用。
1. 然后在"身份提供商"下，然后选中"用户 ID 注册"（这可能说"电子邮件注册"），然后单击"确定"。 
1. 在"用户属性和声明"下，单击"显示更多..."。然后选择您希望用户输入并在令牌中返回的声明选项。 检查至少"显示名称"和"电子邮件地址"以收集和返回，然后单击"确定"，然后单击"创建"。
1. 选择您在列表中创建的策略，然后单击"运行用户流"按钮。
1. 此操作将打开运行的用户流边栏选项卡，选择前端应用程序，然后记录"选择域"下拉下栏下显示的b2clogin.com域的地址。
1. 单击顶部的链接以打开"众所周知的 openid 配置终结点"，并将authorization_endpoint和token_endpoint值以及链路本身的值记录为众所周知的 openid 配置终结点。

   > [!NOTE]
   > B2C 策略允许您公开 Azure AD B2C 登录终结点，以便能够捕获不同的数据组件，并以不同的方式登录用户。 在这种情况下，我们配置了注册或登录终结点，该终结点公开了已知的配置终结点，特别是我们创建的策略是由 p_ 参数在 URL 中标识的。
   > 
   > 完成此操作后，您现在拥有一个功能化的"企业到消费者"标识平台，该平台将用户登录到多个应用程序。 
   > 如果需要，可以在此处单击"运行用户流"按钮（通过注册或登录过程），并了解它将在实践中执行哪些操作，但结束时的重定向步骤将失败，因为应用尚未部署。

## <a name="build-the-function-api"></a>构建函数 API
1. 在 Azure 门户中切换回标准 Azure AD 租户，以便我们可以再次配置订阅中的项目 
1. 转到 Azure 门户的"功能应用"边栏选项卡，打开空函数应用，然后通过快速入门创建新的门户内"Webhook + API"功能。
1. 将下面的示例代码粘贴到 Run.csx 上，将现有代码粘贴到显示的现有代码上。

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
   > 您刚刚粘贴的 c# 脚本函数代码只需将一行记录到函数日志，即可返回带有一些动态数据的文本"Hello World"（日期和时间）。

3. 从左侧边栏选项卡中选择"集成"，然后在窗格的右上角选择"高级编辑器"。
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

5. 切换回 HttpTrigger1 选项卡，单击"获取函数 URL"，然后复制显示的 URL。

   > [!NOTE]
   > 您刚刚创建的绑定只需告诉函数以匿名 http GET 请求响应您刚刚复制的 URL。 （https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey)现在我们有一个可扩展的无服务器 https API，它能够返回非常简单的有效负载。
   > 现在，您可以使用上面的 URL 测试从 Web 浏览器调用此 API，还可以剥离 URL 的 ？code_机密部分，并证明 Azure 函数将返回 401 错误。

## <a name="configure-and-secure-the-function-api"></a>配置并保护功能 API
1. 需要配置功能应用中的两个额外的区域（Auth 和网络限制）。
1. 首先，让我们配置身份验证/授权，因此单击函数应用的名称&lt;（Z&gt;函数图标旁边）以显示概览页面。
1. 下一步选择"平台功能"选项卡，然后选择"身份验证/授权"。
1. 打开应用服务身份验证功能。
1. 在"身份验证提供程序"下选择"Azure 活动目录"，并从管理模式开关中选择"高级"。
1. 将后端函数 API 的应用程序 ID（从 Azure AD B2C 粘贴到"客户端 ID"框中）
1. 将注册或登录策略中的已知打开 ID 配置终结点粘贴到颁发者 URL 框（我们之前记录了此配置）。
1. 选择“确定”。
1. 将请求未经过身份验证下拉列表时执行的操作设置为"使用 Azure 活动目录登录"，然后单击"保存"。

   > [!NOTE]
   > 现在，函数 API 已部署，如果未提供正确的密钥，则应引发 401 响应，并在显示有效请求时返回数据。
   > 通过配置"使用 Azure AD 登录"选项来处理未经身份验证的请求，在 EasyAuth 中添加了其他深度防御安全性。 请注意，这将更改后端功能应用和前端 SPA 之间的未经授权的请求行为，因为 EasyAuth 将发出 302 重定向到 AAD 而不是 401 未授权响应，我们将稍后使用 API 管理更正此问题。
   > 我们仍然没有应用 IP 安全，如果您有有效的密钥和 OAuth2 令牌，任何人都可以从任何位置调用它 - 理想情况下，我们希望强制所有请求通过 API 管理来。
   > 如果使用 API 管理使用层，则由于该层没有专用的静态 IP，因此无法由 VIP 执行此锁定，则需要依赖通过共享密钥功能键锁定 API 调用的方法，因此步骤 11-13 将是不可能的。

1. 关闭"身份验证/授权"边栏选项卡 
1. 选择"网络"，然后选择"访问限制"
1. 接下来，将允许的功能应用 IP 锁定到 API 管理实例 VIP。 此 VIP 显示在门户的 API 管理 - 概述部分。
1. 如果要继续与函数门户交互，并执行以下可选步骤，还应在此处添加您自己的公共 IP 地址或 CIDR 范围。
1. 在列表中有允许条目后，Azure 会添加隐式拒绝规则以阻止所有其他地址。 

您需要将 CIDR 格式化的地址块添加到 IP 限制面板中。 当您需要添加单个地址（如 API 管理 VIP）时，您需要将其添加到格式 xx.xx.xx.xx 中。

   > [!NOTE]
   > 现在，除了通过 API 管理或您的地址之外，不应从任何其他位置调用函数 API。
   
## <a name="import-the-function-app-definition"></a>导入函数应用定义
1. 打开*API 管理边栏选项卡*，然后打开*实例*。
1. 从实例的 API 管理部分选择 API 刀片。
1. 在"添加新 API"窗格中，选择"功能应用"，然后从弹出窗口顶部选择"完整"。
1. 单击"浏览"，选择您托管 API 的功能应用，然后单击"选择"。
1. 为 API 提供 API 管理内部使用的名称和说明，并将其添加到"无限制"产品中。
1. 请确保记录基本 URL 供以后使用，然后单击"创建"。

## <a name="configure-oauth2-for-api-management"></a>为 API 管理配置 Oauth2

1. 接下来，从"安全"选项卡中选择 Oauth 2.0 边栏选项卡，然后单击"添加"
1. 为添加的 Oauth 终结点提供*显示名称*和*说明*的值（这些值将作为 Oauth2 终结点显示在下一步中）。
1. 您可以在客户端注册页 URL 中输入任何值，因为不会使用此值。
1. 检查*隐式 Auth*授予类型，并保留"授权代码授予"类型选中。
1. 移动到*授权*和*令牌*终结点字段，然后输入之前从已知配置 xml 文档中捕获的值。
1. 使用 Azure AD B2C 应用注册中的后端函数 API 客户端 ID 向下滚动并填充称为"资源"*的其他正文参数*
1. 选择"客户端凭据"，将客户端 ID 设置为开发人员控制台应用的应用 ID - 如果使用消耗 API 管理模型，请跳过此步骤。
1. 将客户端密钥设置为前面录制的密钥 - 如果使用消耗 API 管理模型跳过此步骤。
1. 最后，现在记录 API 管理授予的 auth 代码redirect_uri，以便以后使用。

## <a name="set-up-oauth2-for-your-api"></a>为 API 设置 Oauth2
1. API 将显示在门户左侧的"所有 API"部分下，通过单击它打开 API。
1. 选择"设置"选项卡。
1. 通过从用户授权单选按钮中选择"Oauth 2.0"来更新设置。
1. 选择您之前定义的 Oauth 服务器。
1. 选中"覆盖范围"复选框，然后输入您为后端 API 调用记录的范围。

   > [!NOTE]
   > 现在，我们有一个 API 管理实例，它知道如何从 Azure AD B2C 获取访问令牌以授权请求，并了解我们的 Oauth2 Azure 活动目录 B2C 配置。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>设置**CORS**和**验证 jwt**策略

> 无论使用 APIM 层，都应遵循以下各节。 

1. 切换回设计选项卡并选择"所有 API"，然后单击代码视图按钮以显示策略编辑器。
1. 编辑入站部分并粘贴下面的 xml，以便它读如下。

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
1. 编辑 openid 配置 URL 以匹配众所周知的 Azure AD B2C 终结点，以便注册或登录策略。
1. 编辑声明值以匹配有效的应用程序 ID（也称为后端 API 应用程序的客户端 ID）并保存。

   > [!NOTE]
   > 现在，API 管理能够响应对 JS SPA 应用的跨源请求，它将执行对 JWT 身份验证令牌的限制、速率限制和预验证，然后再将请求转发到函数 API。

   > [!NOTE]
   > 以下部分是可选的，不适用于不支持开发人员门户的 **"消耗"** 层。
   > 如果您不打算使用开发人员门户，或者由于使用消耗层而无法使用它，请跳过此步骤并直接跳转到["构建 JavaScript SPA 以使用 API"。](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[可选]配置开发人员门户

1. 打开 Azure AD B2C 边栏选项卡，然后导航到开发人员门户的应用程序注册
1. 将"回复 URL"条目设置为您在之前在 API 管理中配置 redirect_uri 代码授予时记下的条目。

   现在，OAuth 2.0 用户授权在 启用`Echo API`了 ，开发人员控制台在调用 API 之前为用户获取访问令牌。

1. 浏览到开发人员门户中的任何`Echo API`操作，然后选择 **"试用"** 以将您带到开发人员控制台。
1. 请注意 **"授权"** 部分中的新项目，该项与您刚刚添加的授权服务器相对应。
1. 从授权下拉列表中选择**授权代码**，系统将提示您登录到 Azure AD 租户。 如果您已使用该帐户登录，则可能不会提示您。
1. 成功登录后，将标头`Authorization: Bearer`添加到请求中，并在 Base64 中编码来自 Azure AD B2C 的访问令牌。 
1. 选择 **"发送**"，您可以成功调用 API。

   > [!NOTE]
   > 现在，API 管理能够为开发人员门户获取令牌来测试 API，并能够理解它的定义并在开发门户中呈现相应的测试页。

1. 在 API 管理门户的概述边栏选项卡中，单击"开发人员门户"以 API 管理员身份登录。
1. 在这里，您和其他选定的 API 使用者可以从控制台测试和调用它们。
1. 选择"产品"，然后选择"无限制"，然后选择我们之前创建的 API，然后单击"尝试 IT"
1. 取消隐藏 API 订阅密钥，并将其复制到安全的地方以及以后需要的请求 URL。
1. 此外，还可以从"auth auth"下拉列表中选择"隐式"，您可能需要在此处使用弹出窗口进行身份验证。
1. 单击"发送"，如果一切正常，您的功能应用程序应通过 API 管理回复 hello 消息，并带有 200 OK 消息和一些 JSON。

   > [!NOTE]
   > 恭喜您，您现在让 Azure AD B2C、API 管理和 Azure 函数协同工作以发布、保护和使用 API。 您可能已经注意到，API 实际上使用此方法保护了两次，一次使用 API 管理 Ocp-订阅密钥标头，一次使用授权：承载 JWT。
   > 您是正确的，因为此示例是 JavaScript 单页应用程序，我们仅对速率限制和计费调用使用 API 管理密钥。
   > 实际授权和身份验证由 Azure AD B2C 处理，并封装在 JWT 中，JWT 两次验证，一次由 API 管理验证，然后由 Azure 函数验证。

## <a name="build-the-javascript-spa-to-consume-the-api"></a>构建 JavaScript SPA 以使用 API
1. 在 Azure 门户中打开存储帐户边栏选项卡 
1. 选择您创建的帐户，并从"设置"部分选择"静态网站"边栏选项卡（如果您没有看到"静态网站"选项，请检查您创建了 V2 帐户）。
1. 将静态 Web 托管功能设置为"已启用"，并将索引文档名称设置为"index.html"，然后单击"保存"。
1. 记下主终结点的内容，因为此位置是前端站点的托管位置。 

   > [!NOTE]
   > 您可以使用 Azure Blob 存储 + CDN 重写或 Azure 应用服务 - 但 Blob 存储的静态网站托管功能为我们提供了一个默认容器，用于从 Azure 存储中提供静态 Web 内容 /html / js / css，并将为我们推断为零工作的默认页面。

## <a name="upload-the-js-spa-sample"></a>上传 JS SPA 示例
1. 仍在存储帐户边栏选项卡中，从 Blob 服务部分中选择"Blobs"边栏选项卡，然后单击右侧窗格中显示的$web容器。
1. 将以下代码作为 index.html 保存到计算机上的本地文件，然后将文件索引.html 上载到$web容器。

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

1. 浏览到上一节中较早之前存储的静态网站主终结点。

   > [!NOTE]
   > 恭喜您，您刚刚将 JavaScript 单页应用部署到 Azure 存储，因为我们还没有使用 api 的密钥配置 JS 应用，或者尚未使用 Azure AD B2C 详细信息配置 JS 应用 - 如果打开该页面，该页面将不起作用。

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>为 Azure AD B2C 配置 JS SPA
1. 现在我们知道一切在哪里：我们可以配置SPA与适当的API管理API地址和正确的Azure AD B2C应用程序/客户端ID
1. 返回 Azure 门户存储边栏选项卡并单击 index.html，然后选择"编辑 Blob" 
1. 更新 auth 详细信息以匹配之前在 B2C 中注册的前端应用程序，并注意到"b2cScopes"值适用于 API 后端。
1. 可以在 API 管理测试窗格中找到 WEBApi 密钥和 api URL。
1. 通过返回 API 管理回 API 管理边栏选项卡，选择"订阅"，然后单击"添加订阅"然后保存记录，创建 APIM 订阅密钥。 单击创建行旁边的椭圆 （...） 将允许您显示键，以便可以复制主键。
1. 它应该类似于下面的代码：-  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>为 Azure AD B2C 前端应用设置重定向 URI
1. 打开 Azure AD B2C 边栏选项卡，然后导航到 JavaScript 前端应用程序的应用程序注册
1. 将重定向 URL 设置为之前设置上面静态网站主终结点时记下的 URL

   > [!NOTE] 
   > 此配置将导致前端应用程序的客户端接收具有 Azure AD B2C 适当声明的访问令牌。
   > SPA 将能够将其添加为后端 API 调用中的 https 标头中的承载令牌。
   > API 管理将在将请求传递到接收的 Azure 函数 API 之前，通过订阅方密钥预先验证对终结点的令牌、速率限制调用。
   > SPA 将在浏览器中呈现响应。

   > *恭喜您，您已配置 Azure AD B2C、Azure API 管理、Azure 功能、Azure 应用服务授权以完美和谐地工作！*

   > [!NOTE]
   > 现在，我们有一个简单的应用程序与一个简单的安全API，让我们测试它。

## <a name="test-the-client-application"></a>测试客户端应用程序
1. 打开您从之前创建的存储帐户中记下的示例应用 URL
1. 单击右上角的"登录"，此单击将弹出 Azure AD B2C 注册或登录配置文件。
1. 屏幕"登录为"部分的后登录将从 JWT 填充。
1. 现在单击"调用 Web Api"，页面应使用从安全 API 发送回来的值进行更新。

## <a name="and-were-done"></a>我们完成了
可以调整和编辑上述步骤，以便使用 API 管理对 Azure AD B2C 进行许多不同的使用。

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Active Directory 和 OAuth2.0](../active-directory/develop/authentication-scenarios.md)。
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。
* [创建 API 管理服务实例](get-started-create-service-instance.md)。
* [管理第一个 API](import-and-publish.md)。
