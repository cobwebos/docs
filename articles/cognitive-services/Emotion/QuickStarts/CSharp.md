---
title: 快速入门：识别图像中人脸的情感 - 情感 API、C#
titlesuffix: Azure Cognitive Services
description: 获取信息和代码示例，以帮助你通过 C# 快速开始使用情感 API。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 45282c4ad534930fa72a8d8fb71105dca4af23de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218234"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>快速入门：构建应用以识别图像中人脸的情感。

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分正式发布。

本文提供了信息和代码示例，以帮助你通过 C# 快速开始使用[情感 API识别方法](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)。 可用它识别图像中一人或多人表达的情感。

## <a name="prerequisites"></a>先决条件
* 获取的认知服务 [情感 API Windows SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)。
* 获取免费[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="emotion-recognition-c-example-request"></a>情感识别 C# 示例请求

在 Visual Studio 中创建新的控制台解决方案，然后用下面的代码替换 Program.cs。 更改 `string uri`，获取购买订阅密钥时使用的区域。 将 **Ocp-Apim-Subscription-Key** 值替换为有效订阅密钥。 若要查找订阅密钥，请转到 Azure 门户。 在左侧导航窗格的“**密钥**”部分下方，浏览到所需情感 API 资源。 同样，对于“**终结点**”下列出的资源，可在“**概述**”面板中获得内容 URI。

![API 资源密钥](../../media/emotion-api/keys.png)

若要处理请求的响应，请使用 `Newtonsoft.Json` 等库。 这样，可将 JSON 字符串作为一系列成为令牌的可管理对象进行管理。 若要将将此库添加到你的包，请在解决方案资源管理器中右键单击项目，并选择“**管理 Nuget 包**”。 然后搜索“**Newtonsoft**”。 第一个结果应为“**Newtonsoft.Json**”。 选择“安装”。 相克在你的应用程序中引用此库。

![安装 Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); //

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>识别情感示例响应
成功的调用将返回一组人脸条目及其关联表情评分。 按人脸矩形大小降序排列。 空响应指示未检测到任何人脸。 表情条目包含以下字段：

* faceRectangle：图像中人脸的矩形位置
* scores：图像中每张人脸的表情得分

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
