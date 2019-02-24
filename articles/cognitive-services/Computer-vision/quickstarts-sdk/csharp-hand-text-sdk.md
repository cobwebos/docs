---
title: 快速入门：提取手写文本 - SDK、C#
titleSuffix: Azure Cognitive Services
description: 本快速入门将使用计算机视觉 Windows C# 客户端库从图像中提取文本。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d71a566d5c6dc5505b4bd939e294f8428e9a5b93
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312900"
---
# <a name="quickstart-extract-text-using-the-computer-vision-sdk-and-c"></a>快速入门：使用计算机视觉 SDK 和 C# 提取文本

本快速入门将使用适用于 C# 的计算机视觉 SDK 从图像中提取手写文本或印刷体文本。 如果你愿意，可以从 GitHub 上的[认知服务 Csharp 视觉](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision)存储库下载本指南中的代码作为完整的示例应用。

## <a name="prerequisites"></a>先决条件

* 若要使用计算机视觉，需要一个订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。
* 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) 客户端库 NuGet 包。 不需下载程序包。 下面提供了安装说明。

## <a name="create-and-run-the-sample-app"></a>创建并运行示例应用

若要运行此示例，请执行以下步骤：

1. 在 Visual Studio 中创建一个新的 Visual C# 控制台应用。
1. 安装计算机视觉客户端库 NuGet 包。
    1. 在菜单上，单击“工具”，然后依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”。
    1. 单击“浏览”选项卡，在“搜索”框中键入“Microsoft.Azure.CognitiveServices.Vision.ComputerVision”。
    1. 选择显示的 **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**，单击项目名称旁边的复选框，然后单击“安装”。
1. 将 `Program.cs` 替换为以下代码。 `RecognizeTextAsync` 和 `RecognizeTextInStreamAsync` 方法分别为远程图像和本地图像包装[识别文本 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)。 `GetTextOperationResultAsync` 方法包装[获取识别文本操作结果 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201)。

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // For printed text, change to TextRecognitionMode.Printed
            private const TextRecognitionMode textRecognitionMode =
                TextRecognitionMode.Handwritten;

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/" +
                "Cursive_Writing_on_Notebook_paper.jpg/" +
                "800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Recognize text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to recognize the text
                RecognizeTextHeaders textHeaders =
                    await computerVision.RecognizeTextAsync(
                        imageUrl, textRecognitionMode);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    // Start the async process to recognize the text
                    RecognizeTextInStreamHeaders textHeaders =
                        await computerVision.RecognizeTextInStreamAsync(
                            imageStream, textRecognitionMode);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                TextOperationResult result =
                    await computerVision.GetTextOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetTextOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var lines = result.RecognitionResult.Lines;
                foreach (Line line in lines)
                {
                    Console.WriteLine(line.Text);
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `computerVision.Endpoint` 更改为与订阅密钥关联的 Azure 区域。
1. （可选）将 `textRecognitionMode` 设置为 `TextRecognitionMode.Printed`。
1. 将 `<LocalImage>` 替换为本地图像的路径和文件名。
1. （可选）将 `remoteImageUrl` 设置为另一图像。
1. 运行该程序。


## <a name="examine-the-response"></a>检查响应

成功的响应会输出每个图像的各行已识别文本。

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

请参阅[快速入门：提取手写文本 - REST、C#](../QuickStarts/CSharp-hand-text.md#examine-the-response)，以获取 API 调用的原始 JSON 输出的示例。

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和地标、创建缩略图以及提取印刷体文本和手写文本的计算机视觉 API。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
