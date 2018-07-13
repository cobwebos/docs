---
title: 异常情况检测 C# 应用 - Microsoft 认知服务 | Microsoft Docs
description: 介绍使用 Microsoft 认知服务中的异常情况检测 API 的 C# 应用。 将原始数据点发送到 API 并获得预期值和异常点。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366054"
---
# <a name="anomaly-detection-c-application"></a>异常情况检测 C# 应用程序

探索一个使用异常情况检测 API 来检测输入异常的基本 Windows 应用程序。 该示例使用订阅密钥将时序数据提交到异常情况检测 API，然后从 API 获取每个数据点的所有异常点和预期值。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

该示例是使用 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) 针对 .NET Framework 而开发的。 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>订阅异常情况检测并获取订阅密钥 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>获取并使用示例

可以将异常情况检测示例应用程序从 [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git) 克隆到计算机。 
<a name="Step1"></a>
### <a name="install-the-example"></a>安装示例

在 GitHub 桌面，打开 Sample\AnomalyDetectionSample.sln。

<a name="Step2"></a>
### <a name="build-the-example"></a>生成示例

按 Ctrl+Shift+B 或单击功能区菜单上的“生成”，然后选择“生成解决方案”。

<a name="Step3"></a>
### <a name="run-the-example"></a>运行示例

1. 生成完成后，按 F5 或单击功能区菜单上的“启动”来运行该示例。
2. 使用读取“{your_subscription_key}”的文本编辑框找到异常情况检测用户界面窗口。
3. 将包含示例数据的 Request.json 文件替换为你自己的数据，然后单击“发送”按钮。 Microsoft 会收到你上传的数据，并使用它们来检测其中的任何异常点。 更新的数据不会保留在 Microsoft 服务器中。 若要再次检测异常点，需要再次上传数据。
4. 如果数据正常，将在“响应”字段中找到异常情况检测结果。 如果出现任何错误，错误信息同样会显示在“响应”字段中。

<a name="Review"></a>
### <a name="read-the-result"></a>读取结果

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>温故知新

既然现在应用程序已经在运行了，那我们来回顾一下这个示例应用是如何与认知服务技术集成的。 这样用户能更轻松地继续构建此应用或是使用 Microsoft 异常情况检测开发自己的应用。

此示例应用采用了异常情况检测 Restful API 终结点。

我们来看一看 AnomalyDetectionClient.cs 中的一个代码片段，回顾一下 Restful API 在示例应用程序中的用法。 该文件包含代码注释，指示“KEY SAMPLE CODE STARTS HERE”和“KEY SAMPLE CODE ENDS HERE”帮助用户查找下方呈现的代码片段。

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
Request(...) 下面的代码片段演示如何使用 HttlClient 将订阅密钥和数据点提交到异常情况检测 API 的终结点。

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
