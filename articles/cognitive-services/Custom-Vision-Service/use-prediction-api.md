---
title: 示例：使用分类器和预测终结点以编程的方式测试图像 - 自定义视觉
titlesuffix: Azure Cognitive Services
description: 了解如何借助自定义影像服务分类器使用 API 以编程方式测试图像。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: article
ms.date: 03/26/2019
ms.author: anroth
ms.openlocfilehash: 715fa526c83608c9922315e3a0d89b67b31e0d16
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472713"
---
#  <a name="use-your-model-with-the-prediction-api"></a>您的模型使用预测 API

训练模型后，可以通过将图像提交到预测 API 来以编程方式测试这些图像。

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关 API 用法的详细信息和示例，请参阅[预测 API 参考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>发布已训练的迭代

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡。

若要提交映像分发到预测 API，你将首先需要发布用于预测，这可以通过选择迭代__发布__和指定的名称的已发布的迭代。 这将使您的模型可以预测 api 自定义视觉 Azure 资源的访问。 

![用红色矩形围绕发布按钮，显示性能选项卡。](./media/use-prediction-api/unpublished-iteration.png)

已成功发布您的模型之后, 你将看到显示在左侧边栏中，以及小版本的说明中已发布的迭代名称迭代旁边的"已发布"标签。

![显示性能选项卡，用红色矩形围绕的已发布的标签和已发布的迭代的名称。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

一旦已发布您的模型，可以检索有关使用预测 API，通过选择信息__预测 URL__。 这会打开一个对话框，类似如下所示使用预测 API 的信息包括__预测 URL__并__预测密钥__。

![性能选项卡显示一个红色矩形围绕预测 URL 按钮。](./media/use-prediction-api/published-iteration-prediction-url.png)

![性能选项卡显示一个红色矩形周围使用的图像文件和预测密钥值的预测 URL 值。](./media/use-prediction-api/prediction-api-info.png)

> [!TIP]
> 你__预测键__还可在[Azure 门户](https://portal.azure.com)页上自定义视觉 Azure 资源关联到你的项目，在__密钥__。 

从对话框中，应用程序中的复制使用的以下信息：

* __预测 URL__是用于__映像文件__。
* __预测密钥__值。

## <a name="create-the-application"></a>创建应用程序

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。

1. 使用以下代码作为“Program.cs”文件的主体。

    > [!IMPORTANT]
    > 更改以下信息：
    >
    > * 将“命名空间”设置为项目名称。
    > * 设置__预测键__数值前面的开头的行检索`client.DefaultRequestHeaders.Add("Prediction-Key",`。
    > * 设置__预测 URL__数值前面的开头的行检索`string url =`。

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
                client.DefaultRequestHeaders.Add("Prediction-Key", "3b9dde6d1ae1453a86bfeb1d945300f2");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "https://southcentralus.api.cognitive.microsoft.com/customvision/v3.0/Prediction/8622c779-471c-4b6e-842c-67a11deffd7b/classify/iterations/Cats%20vs.%20Dogs%20-%20Published%20Iteration%203/image";

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

## <a name="use-the-application"></a>使用应用程序

运行程序时，你将在控制台中的图像文件中输入的路径。 图像提交到预测 API 和预测结果返回为 JSON 文档。 以下 JSON 是响应的示例。

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

[导出模型供移动设备使用](export-your-model.md)

[开始使用.NET Sdk](csharp-tutorial.md)

[开始使用 Python Sdk](python-tutorial.md)

[开始使用 Java Sdk](java-tutorial.md)

[开始使用 Node Sdk](node-tutorial.md)

[开始使用 Go Sdk](go-tutorial.md)
