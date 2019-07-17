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
ms.openlocfilehash: 3b408de6b60e7e7704ee228b52c399e5b80e3a9e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718417"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>快速入门：创建用于启动沉浸式阅读器的 Web 应用 (C#)

[沉浸式读取器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术以提高阅读理解能力。

在本快速入门中，你将从头开始构建一个 Web 应用，并使用沉浸式阅读器 SDK 集成沉浸式阅读器。 本快速入门的完整工作示例可在[此处](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp)找到。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* 沉浸式阅读器的订阅密钥。 按照[这些说明](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)获取一个订阅密钥。

## <a name="create-a-web-app-project"></a>创建 Web 应用项目

在 Visual Studio 中使用内置“模型-视图-控制器”的 ASP.NET Core Web 应用程序模板创建一个新项目。

![新建项目](./media/vswebapp.png)

![新建 ASP.NET Core Web 应用程序](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>获取访问令牌

在下一步中，需要订阅密钥和终结点。 可以在 Azure 门户中的沉浸式阅读器资源的“秘钥”页中找到订阅密钥。 可以在“概述”页中找到终结点。

右键单击_解决方案资源管理器_中的项目，然后选择“管理用户机密”  。 这将打开一个名为 _secrets.json_ 的文件。 将该文件的内容替换为以下内容，并在适当的位置提供订阅密钥和终结点。

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

打开 _Controllers\HomeController.cs_，并使用以下代码替换 `HomeController` 类。

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
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
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
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

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
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

* 查看[教程](./tutorial.md)，了解使用沉浸式阅读器 SDK 还可以做什么
* 探索[沉浸式阅读器 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和[沉浸式阅读器 SDK 参考](./reference.md)
