---
title: 使用自定义影像服务预测终结点 - Azure 认知服务 | Microsoft Docs
description: 了解如何借助自定义影像服务分类器使用 API 以编程方式测试图像。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 54f9d9fec1f40c167341dec6a8699b6a558419da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366035"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>借助自定义影像服务分类器使用预测终结点以编程方式测试图像

训练模型后，可以通过将图像提交到预测 API 来以编程方式测试这些图像。 

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关 API 用法的详细信息和示例，请参阅[预测 API 参考](https://go.microsoft.com/fwlink/?linkid=865445)。

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡。若要显示有关预测 API 用法的信息，请选择“预测 URL”。 复制要在应用程序中使用的以下信息：

* 使用图像文件的 URL。
* “Prediction-key”值。

> [!TIP]
> 如果具有多个迭代，则可以通过将某个迭代设置为默认，控制使用的迭代。 从“迭代”部分选择该迭代，然后在页面顶部选择“设为默认”。

![显示性能选项卡，其预测 URL 周围有红色矩形框。](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>创建应用程序

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。

2. 使用以下代码作为“Program.cs”文件的主体。

    > [!IMPORTANT]
    > 更改以下信息：
    >
    > * 将“命名空间”设置为项目名称。
    > * 设置之前在以 `client.DefaultRequestHeaders.Add("Prediction-Key",` 开头的行中接收到的“Prediction-Key”值。
    > * 设置之前在以 `string url =` 开头的行中接收到的“URL”值。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

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
        }
    }
    ```

## <a name="use-the-application"></a>使用应用程序

运行应用程序时，输入图像文件的路径。 图像将提交到 API，结果将以 JSON 文档的形式返回。 以下 JSON 是响应的示例

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>后续步骤

[导出模型，供移动使用](export-your-model.md)