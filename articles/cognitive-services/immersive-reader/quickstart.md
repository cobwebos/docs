---
title: 快速入门：创建使用 C# 启动沉浸式阅读器的 Web应用
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将从头开始构建一个 Web 应用，并添加沉浸式阅读器 API 功能。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845243"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>快速入门：创建用于启动沉浸式阅读器的 Web 应用 (C#)

[沉浸式阅读器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术以提高阅读理解能力。

在本快速入门中，你将从头开始构建一个 Web 应用，并使用沉浸式阅读器 SDK 集成沉浸式阅读器。 本快速入门的完整工作示例可在[此处](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)找到。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置示例项目属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。

## <a name="create-a-web-app-project"></a>创建 Web 应用项目

在 Visual Studio 中使用具有内置“模型-视图-控制器”的 ASP.NET Core Web 应用程序模板和 ASP.NET Core 2.1 创建一个新项目。 将该项目命名为“QuickstartSampleWebApp”。

![新建项目](./media/quickstart-csharp/1-createproject.png)

![配置新项目](./media/quickstart-csharp/2-configureproject.png)

![新建 ASP.NET Core Web 应用程序](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>设置身份验证

### <a name="configure-authentication-values"></a>配置身份验证值

右键单击_解决方案资源管理器_中的项目，然后选择“管理用户机密”  。 这将打开一个名为 _secrets.json_ 的文件。 此文件未签入到源代码管理中。 在[此处](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows)了解更多信息。 将 _secrets.json_ 的内容替换为以下内容，并提供在创建沉浸式阅读器资源时给出的值。

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>添加 Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 包

以下代码使用 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 包中的对象，因此将需要在项目中添加对该包的引用。

从“工具”->“NuGet 包管理器”->“包管理器控制台”  打开 NuGet 包管理器控制台，并运行以下命令：

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>更新控制器以获取令牌 

打开 _Controllers\HomeController.cs_，然后在该文件顶部的 _using_ 语句后添加以下代码。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

现在，我们将配置控制器以从 _secrets.json_ 获取 Azure AD 值。 在 _HomeController_ 类的顶部，在 ```public class HomeController : Controller {``` 之后添加以下代码。

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>添加示例内容
首先，打开 _Views\Shared\Layout.cshtml_。 在行 ```</head>``` 之前，添加以下代码：

```html
@RenderSection("Styles", required: false)
```

现在，我们将向此 Web 应用添加示例内容。 打开 _Views\Home\Index.cshtml_，并将所有自动生成的代码替换为以下示例：

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

请注意，所有文本都有一个 **lang** 属性，该属性描述了文本的语言。 此属性可帮助沉浸式阅读器提供相关的语言和语法功能。

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>添加 JavaScript 以处理启动沉浸式阅读器

沉浸式阅读器库提供了启动沉浸式阅读器和呈现沉浸式阅读器按钮等功能。 在[此处](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference)了解更多信息。

在 _Views\Home\Index.cshtml_ 的底部，添加以下代码：

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>生成并运行应用

在菜单栏中，选择“调试”>“开始调试”  ，或按 **F5** 启动应用程序。

在浏览器中，应该看到：

![示例应用](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>启动沉浸式阅读器

单击“沉浸式阅读器”按钮时，将会看到沉浸式阅读器随页面上的内容一起启动。

![沉浸式阅读器](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](./quickstart-nodejs.md)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Python 教程](./tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [iOS 教程](./tutorial-ios-picture-immersive-reader.md)，了解通过 Swift 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)