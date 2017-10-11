---
title: "使用 Azure Active Directory 和 API 管理保护 Web API 后端 | Microsoft 文档"
description: "了解如何使用 Azure Active Directory 和 API 管理保护 Web API 后端。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: f856ff03-64a1-4548-9ec4-c0ec4cc1600f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 0dfb4102904c2e972e6617fd3851fb1c50147357
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>如何使用 Azure Active Directory 和 API 管理保护 Web API 后端
下面的视频演示了如何生成 Web API 后端，并使用具有 Azure Active Directory 和 API 管理的 OAuth 2.0 协议对其进行保护。  本文提供视频中步骤的概述及其他信息。 此视频演示了以下内容，时长 24 分钟：

* 如何生成 Web API 后端并使用 AAD 进行保护 - 从 1:30 开始
* 如何将 API 导入到 API 管理 - 从 7:10 开始
* 如何配置开发人员门户以调用 API - 从 9:09 开始
* 如何配置桌面应用程序以调用 API - 从 18:08 开始
* 如何配置 JWT 验证策略以对请求进行预授权 - 从 20:47 开始

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

## <a name="create-an-azure-ad-directory"></a>创建 Azure AD 目录
若要使用 Azure Active Directory 保护 Web API 后端，必须首先创建 AAD 租户。 本视频中使用租户 **APIMDemo**。 若要创建 AAD 租户，请登录 [Azure 经典门户](https://manage.windowsazure.com)，单击“新建”->“应用服务”->“Active Directory”->“目录”->“自定义创建”。 

![Azure Active Directory][api-management-create-aad-menu]

此示例中使用默认域 **DemoAPIM.onmicrosoft.com** 创建目录 **APIMDemo**。 整个视频都将使用此目录。

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>创建由 Azure Active Directory 保护的 Web API 服务
此步骤使用 Visual Studio 2013 创建 Web API 后端。 视频中的此步骤从 1:30 开始。 要在 Visual Studio 中创建 Web API 后端项目，单击“文件”->“新建”->“项目”，并从“Web”模板列表中选择“ASP.NET Web 应用程序”。 在本视频中，此项目的名称为 **APIMAADDemo**。 单击“确定”以创建该项目  。 

![Visual Studio][api-management-new-web-app]

单击“选择模板”列表中的“Web API”，创建 Web API 项目。 若要配置 Azure Directory 身份验证，请单击“更改身份验证”。

![新建项目][api-management-new-project]

单击“组织帐户”，并指定 AAD 租户的“域”。 此示例中的域是 **DemoAPIM.onmicrosoft.com**。 可从目录的“域”选项卡获取域的目录。

![域][api-management-aad-domains]

在“更改身份验证”对话框中配置所需设置，并单击“确定”。

![更改身份验证][api-management-change-authentication]

单击“确定”后，Visual Studio 将尝试向 Azure AD 目录注册应用程序，并且系统可能会提示通过 Visual Studio 登录。 使用目录的管理帐户登录。

![登录 Visual Studio][api-management-sign-in-vidual-studio]

要将此项目配置为 Azure Web API，请选中“在云中托管”，然后单击“确定”。

![新建项目][api-management-new-project-cloud]

系统可能会提示需要登录 Azure 才能配置 Web 应用。

![配置][api-management-configure-web-app]

此示例中指定了一个新的“应用服务计划”**APIMAADDemo**。

单击“确定”配置 Web 应用和创建项目。

## <a name="add-the-code-to-the-web-api-project"></a>将代码添加到 Web API 项目
视频的下一步是将代码添加到 Web API 项目。 此步骤从 4:35 开始。

此示例中的 Web API 使用模型和控制器实现基本计算器服务。 要为服务添加模型，请右键单击“解决方案资源管理器”中的“模型”，并依次选择“添加”、“类”。 将类命名为 `CalcInput`，并单击“添加”。

将下面的 `using` 语句添加到 `CalcInput.cs` 文件顶部。

```c#
using Newtonsoft.Json;
```

将生成的类替换为以下代码。

```c#
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

右键单击“解决方案资源管理器”中的“Controllers”文件夹，并选择“添加”->“控制器”。 选择“Web API 2 控制器 - 空”，并单击“添加”。 键入 **CalcController** 用作控制器名称，并单击“添加”。

![添加控制器][api-management-add-controller]

将下面的 `using` 语句添加到 `CalcController.cs` 文件顶部。

```c#
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

将生成的控制器替换为以下代码。 此代码实施基本计算器 API 的 `Add`、`Subtract`、`Multiply` 和 `Divide` 操作。

```c#
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

按 **F6** 生成并验证解决方案。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure
此步骤中将 Visual Studio 项目发布到 Azure。 视频中的此步骤从 5:45 开始。

要将项目发布到 Azure，请右键单击 Visual Studio 中的“APIMAADDemo”项目，并选择“发布”。 保留“发布 Web”对话框中的默认设置，并单击“发布”。

![Web 发布][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>向 Azure AD 后端服务应用程序授予权限
在配置和发布 Web API 项目的过程中，会在 Azure AD 目录中创建适用于后端服务的新应用程序。 此步骤将向 Web API 后端授予权限，在视频中从 6:13 开始。

![应用程序][api-management-aad-backend-app]

单击要配置的应用程序的名称和所需权限。 导航“配置”选项卡，向下滚动到“对其他应用程序的权限”部分。 单击**应用程序权限**旁边的下拉列表**Windows** **Azure Active Directory**，选中对应的框**读取目录数据**，然后单击**保存**。

![添加权限][api-management-aad-add-permissions]

> [!NOTE]
> 如果 **Windows** **Azure Active Directory** 未在“对其他应用程序的权限”下列出，则单击“添加应用程序”将该项添加到列表中。
> 
> 

记下“应用 ID URI”，以便在后续步骤中为 API 管理开发人员门户配置 Azure AD 应用时使用。

![应用 ID URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>将 Web API 导入 API 管理
在 API 发布者门户（通过 Azure 门户访问）中配置 API。 若要访问，请单击 API 管理服务工具栏中的“发布者门户”。 如果尚未创建 API 管理服务实例，请参阅[管理第一个 API][Manage your first API] 教程中的[创建 API 管理服务实例][Create an API Management service instance]。

![发布者门户][api-management-management-console]

可将操作[手动添加到 API](api-management-howto-add-operations.md)，也可以导入操作。 在此视频中，以 Swagger 格式导入操作，从 6:40 开始。

创建包含以下内容的文件 `calcapi.json` 并将其保存到计算机。 确保 `host` 属性指向 Web API 后端。 此示例使用 `"host": "apimaaddemo.azurewebsites.net"`。

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

要导入计算器 API，请单击左侧“API 管理”菜单中的“API”，并单击“导入 API”。

![“导入 API”按钮][api-management-import-api]

执行以下步骤配置计算器 API。

1. 单击“从文件”，浏览到保存的 `calculator.json` 文件，并单击“Swagger”单选按钮。
2. 在“Web API URL 后缀”文本框中键入“calc”。
3. 单击“产品(可选)”框，并选择“初学者”。
4. 单击“保存”导入 API  。

![添加新的 API][api-management-import-new-api]

导入 API 后，该 API 的摘要页会显示在发布者门户中。

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>从开发人员门户调用 API 失败
此时，该 API 已导入 API 管理，但无法从开发人员门户成功调用，因为后端服务受 Azure AD 身份验证保护。 视频中使用以下步骤对此进行演示，从 7:40 开始。

单击发布者门户右上角中的“开发人员门户” 。

![开发人员门户][api-management-developer-portal-menu]

依次单击“API”和“计算器”API。

![开发人员门户][api-management-dev-portal-apis]

单击“试用”。

![试用][api-management-dev-portal-try-it]

单击“发送”并记下“401 未授权”的响应状态。

![发送][api-management-dev-portal-send-401]

请求未经授权，因为后端 API 受 Azure Active Directory 保护。 要成功调用 API，必须首先将开发人员门户配置为使用 OAuth 2.0 向开发人员授权。 此过程在以下部分中介绍。

## <a name="register-the-developer-portal-as-an-aad-application"></a>将开发人员门户注册为 AAD 应用程序
将开发人员门户配置为使用 OAuth 2.0 向开发人员授权的第一步是：将开发人员门户注册为 AAD 应用程序。 视频中的此演示从 8:27 开始。

通过本视频的第一步导航到 Azure AD 租户（此示例中的租户为“APIMDemo”），并导航到“应用程序”选项卡。

![新建应用程序][api-management-aad-new-application-devportal]

单击“添加”按钮创建新的 Azure Active Directory 应用程序，并选择“添加我的组织正在开发的应用程序”。

![新建应用程序][api-management-new-aad-application-menu]

选择“Web 应用程序和/或 Web API”，输入一个名称，并单击下一步箭头。 此示例中使用“APIMDeveloperPortal”。

![新建应用程序][api-management-aad-new-application-devportal-1]

对于“登录 URL”，请输入 API 管理服务的 URL，并追加 `/signin`。 此示例使用 `https://contoso5.portal.azure-api.net/signin`。

对于“应用 ID URL”，请输入 API 管理服务的 URL，并追加某些唯一字符。 这些类型可以是任何所需字符，在此示例中使用了 `https://contoso5.portal.azure-api.net/dp`。 配置所需“应用属性”后，单击复选标记创建应用程序。

![新建应用程序][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>配置 API 管理 OAuth 2.0 授权服务器
下一步是在 API 管理中配置 OAuth 2.0 授权服务器。 视频中的此步骤演示从 9:43 开始。

在左侧的“API 管理”菜单中单击“安全”，并单击“OAuth 2.0”和“添加授权服务器”。

![添加授权服务器][api-management-add-authorization-server]

在“名称”和“说明”字段中输入名称和可选说明。 这些字段用于标识 API 管理服务实例中的 OAuth 2.0 授权服务器。 此示例中使用“授权服务器演示”。 稍后指定要用于 API 身份验证的 OAuth 2.0 服务器时，需选择此名称。

对于“客户端注册页 URL”，请输入占位符值，如 `http://localhost`。  “客户端注册页 URL”指向供用户针对 OAuth 2.0 提供程序创建和配置其自己的帐户的页面，这些提供程序支持用户管理帐户。 在此示例中，用户不会创建和配置自己的帐户，因此使用占位符。

![添加授权服务器][api-management-add-authorization-server-1]

接下来，指定“授权终结点 URL”和“令牌终结点 URL”。

![授权服务器][api-management-add-authorization-server-1a]

可从为开发人员门户创建的 AAD 应用程序的“应用程序终结点”页面检索这些值。 要访问终结点，请导航到 AAD 应用程序的“配置”选项卡，并单击“查看终结点”。

![应用程序][api-management-aad-devportal-application]

![查看终结点][api-management-aad-view-endpoints]

复制“OAuth 2.0 授权终结点”，并将其粘贴到“授权终结点 URL”文本框。

![添加授权服务器][api-management-add-authorization-server-2]

复制“OAuth 2.0 令牌终结点”，并将其粘贴到“令牌终结点 URL”文本框。

![添加授权服务器][api-management-add-authorization-server-2a]

除了在令牌终结点中粘贴以外，还需添加另一个正文参数 **resource**，其值则使用 AAD 应用程序的“应用 ID URI”，此 AAD 应用程序属于发布 Visual Studio 项目时创建的后端服务。

![应用 ID URI][api-management-aad-sso-uri]

接下来，指定客户端凭据。 这些是想要访问的资源的凭据，此示例中的该资源为开发人员门户。

![客户端凭据][api-management-client-credentials]

要获取“客户端 ID”，请导航到用于开发人员门户的 AAD 应用程序的“配置”选项卡，并复制“客户端 ID”。

要获取“客户端机密”，请单击“密钥”部分中的“选择持续时间”下拉列表，并指定时间间隔。 此示例中使用 1 年。

![客户端 ID][api-management-aad-client-id]

单击“保存”保存配置并显示密钥。 

> [!IMPORTANT]
> 记下此密钥。 关闭 Azure Active Directory 配置窗口后，无法再次显示密钥。
> 
> 

将密钥复制到剪贴板、 切换回发布服务器门户、 将密钥粘贴到**客户端机密**文本框中，单击**保存**。

![添加授权服务器][api-management-add-authorization-server-3]

客户端凭据后紧跟授权代码授予。 复制此授权代码并切换回 Azure AD 开发人员门户应用程序配置页，将授权代码粘贴到“答复 URL”字段，并再次单击“保存”。

![回复 URL][api-management-aad-reply-url]

下一步是配置开发人员门户 AAD 应用程序的权限。 单击“应用程序权限”并选中“读取目录数据”框。 单击“保存”保存此更改，并单击“添加应用程序”。

![添加权限][api-management-add-devportal-permissions]

单击搜索图标，在框的开头键入“APIM”，选择“APIMAADDemo”，并单击复选标记保存。

![添加权限][api-management-aad-add-app-permissions]

单击“APIMAADDemo”的“委派权限”，选中“访问 APIMAADDemo”框，并单击“保存”。 这时开发人员门户应用程序便可访问后端服务。

![添加权限][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>为计算器 API 启用 OAuth 2.0 用户授权
配置 OAuth 2.0 服务器后，便可在安全设置针对 API 指定该服务器。 视频中的此步骤演示从 14:30 开始。

单击左侧菜单中的“API”，并单击“计算器”查看和配置其设置。

![计算器 API][api-management-calc-api]

导航到“安全”选项卡，选中“OAuth 2.0”复选框，从“授权服务器”下拉列表中选择所需的授权服务器，并单击“保存”。

![计算器 API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>成功从开发人员门户调用计算器 API
由于 OAuth 2.0 授权已在 API 上完成配置，因此可从开发人员中心成功调用其操作。 视频中的此步骤演示从 15:00 开始。

在开发人员门户中，导回到计算器服务的“添加两个整数”操作，并单击“试用”。 请注意，“授权”部分中的新项与刚添加的授权服务器对应。

![计算器 API][api-management-calc-authorization-server]

从授权下拉列表中选择“授权代码”，并输入要使用的帐户的凭据。 如果已使用帐户登录，则可能不会出现提示。

![计算器 API][api-management-devportal-authorization-code]

单击“发送”，并记下**响应状态**“200 正常”和响应内容中的操作结果。

![计算器 API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>配置桌面应用程序以调用 API
视频中的下一过程从 16:30 开始，配置一个简单的桌面应用程序来调用 API。 第一步是在 Azure AD 中注册桌面应用程序，并向其授予访问目录和后端服务的权限。 18:25 开始演示桌面应用程序调用计算器 API 上的操作。

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>配置 JWT 验证策略以对请求进行预授权
视频中的最后一个过程从 20:48 开始，演示如何使用[验证 JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) 策略通过验证每个传入请求的访问令牌来对请求进行预授权。 如果请求未通过“验证 JWT”策略进行验证，则将受到 API 管理的阻止且不会传递到后端。

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

有关配置和使用此策略的其他演示，请参阅 [Cloud Cover 第 177 集：更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)并快进到 13:50。 快进到 15:00 若要查看在策略编辑器中配置的策略，然后执行到 18:50 有关从使用或不需要的授权令牌的开发人员门户调用操作的演示。

## <a name="next-steps"></a>后续步骤
* 观看有关 API 管理的更多[视频](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
* 有关保护后端服务的其他方法，请参阅[使用证书进行相互身份验证](api-management-howto-mutual-certificates.md)。

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md
