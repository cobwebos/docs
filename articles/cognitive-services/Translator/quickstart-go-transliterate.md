---
title: 快速入门：转换文本脚本 - 文本翻译，Go
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用文本翻译 API 和 Go 将一种语言的文本从一个脚本转换为另一个脚本。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: c4930e03f5d366eb3f21b44c5be90c20230065c3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126745"
---
# <a name="quickstart-transliterate-text-with-go"></a>快速入门：使用 Go 音译文本

在本快速入门中，你将使用文本翻译 API 将一种语言的文本从一个脚本转换为另一个脚本。

## <a name="prerequisites"></a>先决条件

需要安装 [Go 发行版](https://golang.org/doc/install)才能运行此代码。 本示例代码仅使用**核心**库，因此不需要外部依赖项。

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="transliterate-request"></a>音译请求

以下示例使用 [Transliterate](./reference/v3-0-transliterate.md) 方法将一种语言的文本从一个脚本转换为另一个脚本。

1. 在你喜欢使用的代码编辑器中新建一个 Go 项目。
2. 添加以下提供的代码。
3. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
4. 使用“.go”扩展名保存文件。
5. 在安装了 Go 的计算机上打开命令提示符。
6. 生成文件，例如：“go build quickstart-transliterate.go”。
7. 运行文件，例如：“quickstart-transliterate”。

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/transliterate?api-version=3.0"

    // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script
    const params = "&language=ja&fromScript=jpan&toScript=latn"

    const uri = uriBase + uriPath + params

    // Transliterate "good afternoon".
    const text = "こんにちは"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="transliterate-response"></a>音译响应

成功的响应以 JSON 格式返回，如以下示例所示：

```json
[
  {
    "script": "latn",
    "text": "konnnichiha"
  }
]
```

## <a name="next-steps"></a>后续步骤

从 GitHub 上的 [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 中浏览认知服务 API 的 Go 程序包。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Go 程序包](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
