---
title: 快速入门：计算机视觉 2.0 和 2.1 - 提取印刷体文本和手写文本 - REST、C#
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用计算机视觉 API 和 C# 从图像中提取印刷体文本和手写文本。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b47c0a87f2b7e4f3fea2d5ed088372cabce2a994
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566091"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-20-and-21-rest-api-and-c"></a>快速入门：使用计算机视觉 2.0 和 2.1 REST API 和 C# 提取印刷体文本和手写文本

本快速入门将使用计算机视觉 REST API 从图像中提取印刷体文本和/或手写文本。 使用[批量读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)和[读取操作结果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d)方法，可以检测图像中的文本，并将识别的字符提取到计算机可读的字符流中。 该 API 将确定用于每行文本的识别模型，因此它支持同时包含印刷体文本和手写文本的图像。

与计算机视觉 2.0 和 2.1 相比，计算机视觉 3.0 公共预览版提供：

* 更高的准确度
* 更改的输出格式
* 字词置信度分数
* 通过附加的语言参数同时支持西班牙语和英语

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

> [!IMPORTANT]
> [批量读取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)方法以异步方式运行。 此方法不返回成功响应正文中的任何信息。 相反，批量读取方法返回 `Operation-Location` 响应标头字段值中的 URI。 然后就可以调用此 URI，它表示[读取操作结果](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) API，同时检查状态并返回批量读取方法调用的结果。

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)

> [!IMPORTANT]
> [批量读取](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d986960601faab4bf452005)方法以异步方式运行。 此方法不返回成功响应正文中的任何信息。 相反，批量读取方法返回 `Operation-Location` 响应标头字段值中的 URI。 然后就可以调用此 URI，它表示[读取操作结果](https://westus2.dev.cognitive.microsoft.com/docs/services/5d98695995feb7853f67d6a6/operations/5d9869604be85dee480c8750) API，同时检查状态并返回批量读取方法调用的结果。

---


## <a name="prerequisites"></a>必备条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

- 必须具有 [Visual Studio 2015 或更高版本](https://visualstudio.microsoft.com/downloads/)。
- 必须具有计算机视觉的订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)获取免费的试用密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅计算机视觉并获取密钥。 然后，为密钥和服务终结点字符串[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="create-and-run-the-sample-application"></a>创建和运行示例应用程序

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

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
using System.Linq;
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
        
        // the Batch Read method endpoint
        static string uriBase = endpoint + "vision/v2.1/read/core/asyncBatchAnalyze";

        static async Task Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Text Recognition:");
            Console.Write(
                "Enter the path to an image with text you wish to read: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                await ReadText(imageFilePath);
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with text.</param>
        static async Task ReadText(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Assemble the URI for the REST API method.
                string uri = uriBase;

                HttpResponseMessage response;

                // Two REST API methods are required to extract text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Batch Read, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(uri, content);
                }

                // The response header for the Batch Read method contains the URI
                // of the second method, Read Operation Result, which
                // returns the results of the process in the response body.
                // The Batch Read operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Text
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1);

                if (i == 10 && contentString.IndexOf("\"status\":\"Succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

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

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)

要在 Visual Studio 中创建示例，请执行以下步骤：

1. 使用 Visual C# 控制台应用模板在 Visual Studio 中创建新的 Visual Studio 解决方案。
1. 安装 Newtonsoft.Json NuGet 包。
    1. 在菜单上，单击“工具”，然后依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”    。
    1. 单击“浏览”选项卡，在“搜索”框中键入“Newtonsoft.Json”   。
    1. 选择显示的 Newtonsoft.Json，单击项目名称旁边的复选框，然后单击“安装”   。
1. 运行该程序。
1. 在提示符下，输入本地图像的路径和要识别的语言。

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using System.Web;

namespace CSHttpClientSample
{
    static class Program
    {
        // Add your Computer Vision subscription key and endpoint to your environment variables.
        static string subscriptionKey = Environment.GetEnvironmentVariable("COMPUTER_VISION_SUBSCRIPTION_KEY");

        // An endpoint should have a format like "https://westus.api.cognitive.microsoft.com"
        static string endpoint = Environment.GetEnvironmentVariable("COMPUTER_VISION_ENDPOINT");

        // the Batch Read method endpoint
        static string uriBase = endpoint + "/vision/v3.0-preview/read/analyze";

        static void PrintUsage()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Cognitive Service Batch Read File Sample");
            Console.WriteLine("Usage: ");
            Console.WriteLine("    From Azure Cogntivie Service, retrieve your endpoint and subscription key.");
            Console.WriteLine("    Set environment variable COMPUTER_VISION_ENDPOINT, such as \"https://westus2.api.cognitive.microsoft.com\"");
            Console.WriteLine("    Set environment variable COMPUTER_VISION_SUBSCRIPTION_KEY, such as \"1234567890abcdef1234567890abcdef\"\n");
            Console.WriteLine("    Run the program without argument to enter a file name and a language manually.");
            Console.WriteLine("    Or run the program with a file name for an image file (bmp/jpg/png/tiff) or a PDF file, plus the language. The language can be \"en\" or \"es\".");
            Console.WriteLine("       For example: dotnet Program.dll sample.jpg en");
            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            PrintUsage();

            if (string.IsNullOrEmpty(subscriptionKey) || string.IsNullOrEmpty(endpoint))
            {
                Console.Error.WriteLine("Please set environment variables COMPUTER_VISION_ENDPOINT and COMPUTER_VISION_SUBSCRIPTION_KEY.");
                return;
            }

            string imageFilePath;
            string language;
            if (args.Length == 0)
            {
                Console.Write(
                    "Enter the path to an image (bmp/jpg/png/tiff) or PDF with text you wish to read: ");
                imageFilePath = Console.ReadLine();
            }
            else
            {
                imageFilePath = args[0];
            }

            if (args.Length <= 1)
            {
                Console.Write(
                    "Enter the language to read: \"en\" or \"es\": ");
                language = Console.ReadLine();
            }
            else
            {
                language = args[1];
            }

            Console.WriteLine($"Endpoint:     [{endpoint}]");
            Console.WriteLine($"Subscription: [{subscriptionKey}]");
            Console.WriteLine($"URL:          [{uriBase}]");

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                ReadText(imageFilePath, language).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets the text from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file with text.</param>
        static async Task ReadText(string imageFilePath, string language)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                var builder = new UriBuilder(uriBase);
                builder.Port = -1;
                var query = HttpUtility.ParseQueryString(builder.Query);
                query["language"] = language;
                builder.Query = query.ToString();
                string url = builder.ToString();

                HttpResponseMessage response;

                // Two REST API methods are required to extract text.
                // One method to submit the image for processing, the other method
                // to retrieve the text found in the image.

                // operationLocation stores the URI of the second REST API method,
                // returned by the first REST API method.
                string operationLocation;

                // Reads the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Adds the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // The first REST API method, Batch Read, starts
                    // the async process to analyze the written text in the image.
                    response = await client.PostAsync(url, content);
                }

                // The response header for the Batch Read method contains the URI
                // of the second method, Read Operation Result, which
                // returns the results of the process in the response body.
                // The Batch Read operation does not return anything in the response body.
                if (response.IsSuccessStatusCode)
                    operationLocation =
                        response.Headers.GetValues("Operation-Location").FirstOrDefault();
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                    return;
                }

                // If the first REST API method completes successfully, the second 
                // REST API method retrieves the text written in the image.
                //
                // Note: The response may not be immediately available. Text
                // recognition is an asynchronous operation that can take a variable
                // amount of time depending on the length of the text.
                // You may need to wait or retry this operation.
                //
                // This example checks once per second for ten seconds.
                string contentString;
                int i = 0;
                do
                {
                    System.Threading.Thread.Sleep(1000);
                    response = await client.GetAsync(operationLocation);
                    contentString = await response.Content.ReadAsStringAsync();
                    ++i;
                }
                while (i < 60 && contentString.IndexOf("\"status\":\"succeeded\"") == -1);

                if (i == 60 && contentString.IndexOf("\"status\":\"succeeded\"") == -1)
                {
                    Console.WriteLine("\nTimeout error.\n");
                    return;
                }

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

---

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在控制台窗口中分析和显示成功响应，如下例所示：

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

#### <a name="version-3-public-preview"></a>[版本 3（公共预览版）](#tab/version-3)


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-02-11T16:44:36Z",
  "lastUpdatedDateTime": "2020-02-11T16:44:36Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "es",
        "angle": -0.8011,
        "width": 401,
        "height": 119,
        "unit": "pixel",
        "lines": [
          {
            "language": "es",
            "boundingBox": [
              15,
              42,
              372,
              38,
              373,
              91,
              15,
              97
            ],
            "text": "¡Buenos días!",
            "words": [
              {
                "boundingBox": [
                  15,
                  43,
                  243,
                  40,
                  244,
                  93,
                  17,
                  98
                ],
                "text": "¡Buenos",
                "confidence": 0.56
              },
              {
                "boundingBox": [
                  254,
                  40,
                  370,
                  38,
                  371,
                  91,
                  255,
                  93
                ],
                "text": "días!",
                "confidence": 0.872
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## <a name="clean-up-resources"></a>清理资源

不再需要 Visual Studio 解决方案时，请将其删除。 为此，请打开文件资源管理器，导航到在其中创建 Visual Studio 解决方案的文件夹，然后删除该文件夹。

## <a name="next-steps"></a>后续步骤

探索一个使用计算机视觉执行光学字符识别 (OCR) 的基本 Windows 应用程序。 创建智能裁剪的缩略图；加上对图像中的视觉特征（包括人脸）进行检测、分类、标记和描述。

> [!div class="nextstepaction"]
> [计算机视觉 API C# 教程](../Tutorials/CSharpTutorial.md)
