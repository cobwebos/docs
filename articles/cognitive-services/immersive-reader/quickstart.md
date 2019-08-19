---
title: 快速入门：创建使用 C# 启动沉浸式阅读器的 Web应用
titlesuffix: Azure Cognitive Services
description: 在本快速入门中，你将从头开始构建一个 Web 应用，并添加沉浸式阅读器 API 功能。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 0d67385975db13539c28745eede4fb288a0daedd
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989139"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>快速入门：创建用于启动沉浸式阅读器的 Web 应用 (C#)

[沉浸式读取器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术以提高阅读理解能力。

在本快速入门中，你将从头开始构建一个 Web 应用，并使用沉浸式阅读器 SDK 集成沉浸式阅读器。 本快速入门的完整工作示例可在[此处](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)找到。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* 为 Azure Active Directory (Azure AD) 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./azure-active-directory-authentication.md)进行设置。 在配置示例项目属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。

## <a name="create-a-web-app-project"></a>创建 Web 应用项目

在 Visual Studio 中使用内置“模型-视图-控制器”的 ASP.NET Core Web 应用程序模板创建一个新项目。

![新建项目](./media/vswebapp.png)

![新建 ASP.NET Core Web 应用程序](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>获取 Azure AD 身份验证令牌

此部分需要来自上面的 Azure AD 身份验证配置先决条件步骤的一些值。 请回头参考保存的该会话的文本文件。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

右键单击_解决方案资源管理器_中的项目，然后选择“管理用户机密”  。 这将打开一个名为 _secrets.json_ 的文件。 将该文件的内容替换为以下内容，提供上面的自定义属性值。

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

打开 _Controllers\HomeController.cs_，并使用以下代码替换该文件。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>添加 Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 包

上面的代码使用 **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet 包中的对象，因此需要在项目中添加对该包的引用。

从“工具”->“NuGet 包管理器”->“包管理器控制台”  打开 NuGet 包管理器控制台，然后键入以下内容：

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>添加示例内容

现在，我们将向此 Web 应用添加一些示例内容。 打开 _Views\Home\Index.cshtml_，并将自动生成的代码替换为以下示例：

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>生成并运行应用

在菜单栏中，选择“调试”>“开始调试”  ，或按 **F5** 启动应用程序。

在浏览器中，应该看到：

![示例应用](./media/quickstart-result.png)

单击“沉浸式阅读器”按钮时，将会看到沉浸式阅读器随页面上的内容一起启动。

![沉浸式阅读器](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 教程](./tutorial-nodejs.md)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Python 教程](./tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [iOS 教程](./tutorial-ios-picture-immersive-reader.md)，了解通过 Swift 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)
