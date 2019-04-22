---
title: 使用分类器和预测终结点以编程的方式测试图像 - 自定义视觉
titlesuffix: Azure Cognitive Services
description: 了解如何借助自定义影像服务分类器使用 API 以编程方式测试图像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 1ee6edbf49bbcd2014afcf29ed3b737168a3b5bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046064"
---
# <a name="use-your-model-with-the-prediction-api"></a>您的模型使用预测 API

已训练模型后，可以通过将它们提交到预测 API 终结点以编程方式测试映像。

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关详细信息和示例，请参阅[预测 API 参考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>发布已训练的迭代

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡。

若要提交映像分发到预测 API，你将首先需要发布用于预测，这可以通过选择迭代__发布__和指定的名称的已发布的迭代。 这将使您的模型可以预测 API 自定义视觉 Azure 资源的访问。

![用红色矩形围绕发布按钮，显示性能选项卡。](./media/use-prediction-api/unpublished-iteration.png)

您的模型已成功发布后，您将看到显示在左侧边栏中，迭代旁边的"已发布"标签且其名称将显示在小版本的说明。

![显示性能选项卡，用红色矩形围绕的已发布的标签和已发布的迭代的名称。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

一旦已发布您的模型，可以通过选择检索所需的信息__预测 URL__。 这会打开一个对话框，其中使用预测 API 的信息包括__预测 URL__并__预测密钥__。

![性能选项卡显示一个红色矩形围绕预测 URL 按钮。](./media/use-prediction-api/published-iteration-prediction-url.png)

![性能选项卡显示一个红色矩形周围使用的图像文件和预测密钥值的预测 URL 值。](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 你__预测键__还可在[Azure 门户](https://portal.azure.com)页上自定义视觉 Azure 资源与你的项目，在关联__密钥__边栏选项卡。

在本指南中，您将使用本地映像，因此，请复制下的 URL**如果您有一个图像文件**到临时位置。 复制相应__预测密钥__值。

## <a name="create-the-application"></a>创建应用程序

1. 在 Visual Studio 中，创建一个新C#控制台应用程序。

1. 使用以下代码作为“Program.cs”文件的主体。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 更改以下信息：
   * 设置`namespace`字段为你的项目的名称。
   * 将占位符为`<Your prediction key>`具有前面检索到密钥值。
   * 将占位符为`<Your prediction URL>`前面检索到的 url。

## <a name="run-the-application"></a>运行应用程序

在运行该应用程序时，系统会提示输入到控制台中的图像文件的路径。 该图像然后提交给预测 API，并预测结果返回为 JSON 格式的字符串。 下面是示例响应。

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何提交映像分发到您的自定义图像分类器/检测器和接收响应时以编程方式使用C#SDK。 接下来，了解如何完成端到端方案使用C#，或开始使用不同的语言 SDK。

* [快速入门：.NET SDK](csharp-tutorial.md)
* [快速入门：Python SDK](python-tutorial.md)
* [快速入门：Java SDK](java-tutorial.md)
* [快速入门：Node SDK](node-tutorial.md)
* [快速入门：Go SDK](go-tutorial.md)
