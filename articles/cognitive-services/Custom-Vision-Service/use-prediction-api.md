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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169950"
---
# <a name="use-your-model-with-the-prediction-api"></a>将模型与预测 API 配合使用

训练模型后，可以通过将图像提交到预测 API 终结点以编程方式测试图像。

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关详细信息和示例，请参阅[预测 API 参考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>发布定型的迭代

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡。

若要将图像提交到预测 API，首先需要发布迭代进行预测，这可以通过选择 "__发布__" 并为已发布的迭代指定名称来完成。 这会使你的模型可通过自定义视觉 Azure 资源的预测 API 进行访问。

![显示 "性能" 选项卡，并在 "发布" 按钮的周围显示一个红色矩形。](./media/use-prediction-api/unpublished-iteration.png)

成功发布模型后，你将在左侧边栏中显示 "已发布" 标签，并且其名称将显示在迭代的说明中。

![显示 "性能" 选项卡，并在 "已发布" 标签和已发布的迭代的名称周围显示一个红色矩形。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

在您的模型发布后，您可以通过选择 "__预测 URL__" 来检索所需的信息。 这将打开一个对话框，其中包含有关使用预测 API 的信息，包括__预测 URL__和__预测键__。

!["性能" 选项卡显示在 "预测 URL" 按钮周围显示一个红色矩形。](./media/use-prediction-api/published-iteration-prediction-url.png)

![将显示 "性能" 选项卡，并在 "预测 URL" 值周围显示一个红色矩形，用于使用图像文件和预测键值。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，你将使用本地映像，因此，**如果你有一个图像文件**到临时位置，请复制下的 URL。 同时复制相应的__预测密钥__值。

## <a name="create-the-application"></a>创建应用程序

1. 在 Visual Studio 中，创建一个C#新的控制台应用程序。

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
   * 将 `namespace` 字段设置为项目的名称。
   * 将占位符 `<Your prediction key>` 替换为前面检索到的键值。
   * 将占位符 `<Your prediction URL>` 替换为前面检索到的 URL。

## <a name="run-the-application"></a>运行应用程序

当你运行应用程序时，系统将提示你在控制台中输入图像文件的路径。 然后，将图像提交到预测 API，并将预测结果作为 JSON 格式的字符串返回。 下面是一个示例响应。

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

在本指南中，您学习了如何使用C# SDK 以编程方式将图像提交到自定义图像分类器/检测程序并接收响应。 接下来，了解如何使用C#完成端到端方案，或使用其他语言 SDK 开始使用。

* [快速入门： .NET SDK](csharp-tutorial.md)
* [快速入门： Python SDK](python-tutorial.md)
* [快速入门： Java SDK](java-tutorial.md)
* [快速入门： Node SDK](node-tutorial.md)
* [快速入门：中转 SDK](go-tutorial.md)
