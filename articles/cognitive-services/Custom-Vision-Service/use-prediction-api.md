---
title: 使用分类器和预测终结点以编程的方式测试图像 - 自定义视觉
titleSuffix: Azure Cognitive Services
description: 了解如何借助自定义影像服务分类器使用 API 以编程方式测试图像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: dcb12da680d70e1f0ce4cd763bee340bb3416c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169950"
---
# <a name="use-your-model-with-the-prediction-api"></a>将模型与预测 API 一起使用

训练模型后，可以通过将图像提交到预测 API 终结点来以编程方式测试图像。

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关详细信息和示例，请参阅[预测 API 引用](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>发布经过培训的迭代

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡____。

要将图像提交到预测 API，首先需要发布迭代以供预测，这可以通过选择 __"发布"__ 和指定已发布迭代的名称来完成。 这将使您的模型可供自定义视觉 Azure 资源的预测 API 访问。

![将显示性能选项卡，在"发布"按钮周围有一个红色矩形。](./media/use-prediction-api/unpublished-iteration.png)

成功发布模型后，左侧侧边栏中会出现迭代旁边的"已发布"标签，其名称将显示在迭代说明中。

![将显示性能选项卡，在"已发布"标签周围有一个红色矩形，并显示已发布迭代的名称。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

发布模型后，您可以通过选择__预测 URL__来检索所需的信息。 这将打开一个对话框，其中包含用于使用预测 API 的信息，包括__预测 URL__和__预测键__。

![性能选项卡显示，预测 URL 按钮周围有一个红色矩形。](./media/use-prediction-api/published-iteration-prediction-url.png)

![性能选项卡显示的是一个红色矩形，围绕"预测 URL"值，用于使用图像文件和预测键值。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，您将使用本地图像，因此在 **"如果有图像文件"** 下将 URL 复制到临时位置。 也复制相应的__预测键__值。

## <a name="create-the-application"></a>创建应用程序

1. 在可视化工作室中，创建新的 C# 控制台应用程序。

1. 使用以下代码作为“Program.cs”文件的主体____。

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
   * 将`namespace`字段设置为项目名称。
   * 将占位符`<Your prediction key>`替换为前面检索的键值。
   * 将占位符`<Your prediction URL>`替换为您之前检索的 URL。

## <a name="run-the-application"></a>运行应用程序

运行应用程序时，系统会提示您在控制台中输入映像文件的路径。 然后，图像将提交到预测 API，并将预测结果作为 JSON 格式的字符串返回。 下面是一个示例响应。

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

在本指南中，您学习了如何将图像提交到自定义图像分类器/检测器，以及如何使用 C# SDK 以编程方式接收响应。 接下来，了解如何使用 C# 完成端到端方案，或使用其他语言 SDK 开始。

* [快速入门： .NET SDK](csharp-tutorial.md)
* [快速入门：Python SDK](python-tutorial.md)
* [快速入门：Java SDK](java-tutorial.md)
* [快速入门：节点 SDK](node-tutorial.md)
* [快速入门：转到 SDK](go-tutorial.md)
