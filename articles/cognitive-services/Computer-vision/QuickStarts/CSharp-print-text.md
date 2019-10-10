---
title: 快速入门：提取印刷体文本 (OCR) - REST、C#
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 C# 从图像中提取印刷体文本。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ba0003de0d14827d7bd96298ba591eb3b1e95f44
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177329"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-c"></a>快速入门：使用计算机视觉 REST API 和 C# 提取印刷体文本 (OCR)

在本快速入门中，你将使用计算机视觉的 REST API，通过光学字符识别 (OCR) 从图像中提取印刷体文本。 借助 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 功能，可检测图像中的印刷体文本，并将识别的字符提取到计算机可用的字符流中。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

- 必须具有 [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) 或更高版本。
- 必须具有计算机视觉的订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)获取免费试用密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。 然后，为密钥和服务终结点字符串[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="create-and-run-the-sample-application"></a>创建和运行示例应用程序

要在 Visual Studio 中创建示例，请执行以下步骤：

1. 使用 Visual C# 控制台应用模板在 Visual Studio 中创建新的 Visual Studio 解决方案。
1. 安装 Newtonsoft.Json NuGet 包。
    1. 在菜单上，单击“工具”，然后依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”    。
    1. 单击“浏览”选项卡，在“搜索”框中键入“Newtonsoft.Json”   。
    1. 选择显示的 Newtonsoft.Json，单击项目名称旁边的复选框，然后单击“安装”   。
1. 运行该程序。
1. 在提示符处，输入本地图像的路径。

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");
        
        // the OCR method endpoint
        const string uriBase = endpoint + "vision/v2.1/ocr";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Optical Character Recognition:");
            Console.Write("Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeOCRRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text visible in the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with printed text.</param>
        static async Task MakeOCRRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. 
                // The language parameter doesn't specify a language, so the 
                // method detects it automatically.
                // The detectOrientation parameter is set to true, so the method detects and
                // and corrects text orientation before detecting text.
                string requestParameters = "language=unk&detectOrientation=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Asynchronously get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n\n{0}\n",
                    JToken.Parse(contentString).ToString());
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在控制台窗口中分析和显示成功响应，如下例所示：

```json
{
    "language": "en",
    "textAngle": -1.5000000000000335,
    "orientation": "Up",
    "regions": [
        {
            "boundingBox": "154,49,351,575",
            "lines": [
                {
                    "boundingBox": "165,49,340,117",
                    "words": [
                        {
                            "boundingBox": "165,49,63,109",
                            "text": "A"
                        },
                        {
                            "boundingBox": "261,50,244,116",
                            "text": "GOAL"
                        }
                    ]
                },
                {
                    "boundingBox": "165,169,339,93",
                    "words": [
                        {
                            "boundingBox": "165,169,339,93",
                            "text": "WITHOUT"
                        }
                    ]
                },
                {
                    "boundingBox": "159,264,342,117",
                    "words": [
                        {
                            "boundingBox": "159,264,64,110",
                            "text": "A"
                        },
                        {
                            "boundingBox": "255,266,246,115",
                            "text": "PLAN"
                        }
                    ]
                },
                {
                    "boundingBox": "161,384,338,119",
                    "words": [
                        {
                            "boundingBox": "161,384,86,113",
                            "text": "IS"
                        },
                        {
                            "boundingBox": "274,387,225,116",
                            "text": "JUST"
                        }
                    ]
                },
                {
                    "boundingBox": "154,506,341,118",
                    "words": [
                        {
                            "boundingBox": "154,506,62,111",
                            "text": "A"
                        },
                        {
                            "boundingBox": "248,508,247,116",
                            "text": "WISH"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

浏览一款基本 Windows 应用程序，该应用程序使用计算机视觉执行光学字符识别 (OCR)、创建智能裁剪缩略图，并对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。

> [!div class="nextstepaction"]
> [计算机视觉 API C# 教程](../Tutorials/CSharpTutorial.md)
