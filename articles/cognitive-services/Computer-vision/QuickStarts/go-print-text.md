---
title: 快速入门：提取印刷体文本 - REST、Go
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 Go 从图像中提取印刷体文本。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f79e0ba448ba0ea97305316ea945852d9fab56ad
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206035"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-go-in-computer-vision"></a>快速入门：使用计算机视觉中的 REST API 和 Go 提取印刷体文本 (OCR)

在本快速入门中，你将使用计算机视觉的 REST API，通过光学字符识别 (OCR) 从图像中提取印刷体文本。 借助 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 方法，可检测图像中的印刷体文本，并将识别的字符提取到计算机可用的字符流中。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)。

## <a name="prerequisites"></a>先决条件

- 必须安装有 [Go](https://golang.org/dl/)。
- 必须具有计算机视觉的订阅密钥。 要获取订阅密钥，请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="create-and-run-the-sample"></a>创建并运行示例

要创建和运行示例，请执行以下步骤：

1. 将以下代码复制到文本编辑器中。
1. 必要时在代码中进行如下更改：
    1. 将 `subscriptionKey` 的值替换为你的订阅密钥。
    1. 如有必要，请将 `uriBase` 的值替换为获取的订阅密钥所在的 Azure 区域中的 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 方法的终结点 URL。
    1. （可选）将 `imageUrl` 的值替换为要分析的其他图像的 URL。
1. 将代码保存为以 `.go` 为扩展名的文件。 例如，`get-printed-text.go`。
1. 打开命令提示符窗口。
1. 在提示符处运行 `go build` 命令，对文件中的包进行编译。 例如，`go build get-printed-text.go`。
1. 在提示符处，运行所编译的包。 例如，`get-printed-text`。

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Replace <Subscription Key> with your valid subscription key.
    const subscriptionKey = "<Subscription Key>"

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    const uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr"
    const imageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
        "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png"

    const params = "?language=unk&detectOrientation=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the Http client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the Post request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the Json data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the Json result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "language": "en",
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ],
  "textAngle": 0
}
```

## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除所编译的包以及编译此包所基于的文件，然后关闭命令提示符窗口和文本编辑器。

## <a name="next-steps"></a>后续步骤

了解计算机视觉 API，它用于分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
