---
title: 文本分析 C# 教程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 从 ASP.NET Core Web 应用程序连接到文本分析。
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: eb9730f785b01a620e36a265216488c401eac63a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068482"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>使用 Visual Studio 中连接服务连接到文本分析服务

通过文本分析服务，可提取丰富的信息对可视数据进行分类和处理，并执行计算机辅助的图像审核以帮助策展服务。

本文及其同类文章详细介绍了如何使用文本分析服务的 Visual Studio 连接服务功能。 可在安装了认知服务扩展的 Visual Studio 2017 15.7 或更高版本中使用此功能。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- Visual Studio 2017 版本 15.7（装有 Web 开发工作负荷）。 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>添加对文本分析服务项目的支持

1. 新建一个名为 TextAnalyticsDemo 的 ASP.NET Core Web 项目。 使用具有所有默认设置的 Web 应用程序（模型 - 视图 - 控制器）项目模板。 有必要将项目命名为 MyWebApplication；这样，将代码复制到项目中时，命名空间会匹配。  本文中的示例使用 MVC，但文本分析连接服务可与任何 ASP.NET 项目类型一起使用。

1. 在解决方案资源管理器中，双击“连接服务”项。
   此时显示“连接服务”页，其中包含可添加到项目的服务。

   ![解决方案资源管理器中的连接服务屏幕截图](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. 在可用服务的菜单中，选择“通过文本分析评估情绪”。

   ![“连接的服务”屏幕的屏幕截图](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。

   ![文本分析连接服务屏幕的屏幕截图](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. 选择要使用的订阅，然后选择文本分析服务的名称，或选择“编辑”链接以修改自动生成的名称，再选择资源组和定价层。

   ![资源组和定价层字段的屏幕截图](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   请单击链接，查看定价层的详细信息。

1. 选择“添加”以添加对连接服务的支持。
   Visual Studio 会修改项目来添加 NuGet 包、配置文件条目和其他更改，以支持与文本分析服务的连接。 “输出窗口”显示项目发生情况的日志。 会看到下面这样的内容：

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>使用文本分析服务检测文本示例的语言。

1. 在 Startup.cs 中添加以下 using 语句。
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. 添加配置字段，并添加一个构造函数，该函数初始化 Startup 类中的配置字段以在程序中启用“配置”。

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. 在“控制器”文件夹中添加一个名为 DemoTextAnalyzeController 的类文件，并使用以下代码替换其内容：

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    该代码包含 GetTextAnalyzeClient 和一个请求处理程序，前者获取可用于调用文本分析 API 的客户端对象，后者用于在给定文本上调用 DetectLanguage。

1. 添加上述代码使用的 MyHandler 帮助程序类。

    ```csharp
        class MyHandler : DelegatingHandler
        {
            protected async override Task<HttpResponseMessage> SendAsync(
            HttpRequestMessage request, CancellationToken cancellationToken)
            {
                // Call the inner handler.
                var response = await base.SendAsync(request, cancellationToken);
                
                return response;
            }
        }
    ```

1. 在“模型”文件夹中，为模型添加一个类。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. 添加“视图”以显示分析的文本、确定的语言以及表示分析中置信度的分数。 为此，右键单击“视图”文件夹，选择“添加”，然后选择“视图”。 在出现的对话框中，提供名称 TextAnalyzeResult，接受默认值以在“视图”文件夹中添加名为 TextAnalyzeResult.cshtml 的新文件，并向其复制以下内容：
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. 在本地生成和运行示例。 输入一些文本，查看文本分析检测到的语言。
   
## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除认知服务及相关资源。 若要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到本教程中使用的资源组时，请选择它。
2. 选择“删除资源组”。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

要详细了解文本分析服务，请阅读[文本分析服务文档](index.yml)。
