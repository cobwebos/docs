---
title: 适用于 Azure 认知服务中的文本分析 API 的 Go 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助快速开始使用 Azure 上 Microsoft 认知服务中的文本分析 API。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 08/30/2018
ms.author: nolachar
ms.openlocfilehash: 312bc2e885c2cba42855e907bdcfb0f09ea6f67e
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841705"
---
# <a name="quickstart-for-text-analytics-api-with-go"></a>将文本分析 API 与 Go 配合使用快速入门 
<a name="HOLTop"></a>

本文介绍如何通过 Go 使用[文本分析 API](//go.microsoft.com/fwlink/?LinkID=759711) 来[检测语言](#Detect)、[分析情绪](#SentimentAnalysis)、[提取关键短语](#KeyPhraseExtraction)和[识别链接实体](#Entities)。

有关 API 的技术文档，请参阅 [API 定义](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>先决条件

必须拥有包含**文本分析 API** 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 可使用“每月 5,000 笔交易的免费层”完成此快速入门。

还必须拥有在注册期间生成的[终结点和访问密钥](../How-tos/text-analytics-how-to-access-key.md)。

<a name="Detect"></a>

## <a name="detect-language-request"></a>检测语言请求

语言检测 API 使用[检测语言方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)检测文本文档的语言。

1. 在你喜欢使用的代码编辑器中新建一个 Go 项目。
1. 添加以下提供的代码。
1. 将 `subscriptionKey` 值替换为对你的订阅有效的访问密钥。
1. 将 `uriBase` 中的位置（当前为 `westcentralus`）替换为进行注册的区域。
1. 使用“.go”扩展名保存文件。
1. 在安装了 Go 的计算机上打开命令提示符。
1. 生成文件，例如：“go build quickstart.go”。
1. 运行文件，例如：“quickstart”。

```golang
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
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "ENTER KEY HERE"

    /*
    Replace or verify the region.

    You must use the same region in your REST API call as you used to obtain your access keys.
    For example, if you obtained your access keys from the westus region, replace 
    "westcentralus" in the URI below with "westus".

    NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
    a free trial access key, you should not need to change this region.
    */
    const uriBase = "https://westcentralus.api.cognitive.microsoft.com"
    const uriPath = "/text/analytics/v2.0/"

    // Detect language.
    fmt.Println("===== DETECT LANGUAGE ======")

    uri := uriBase + uriPath + "languages"

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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
```

## <a name="detect-language-response"></a>检测语言响应

在 JSON 中返回成功的响应，如以下示例所示：

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment-request"></a>分析情绪请求

情绪分析 API 使用 [Sentiment 方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9)检测一组文本记录的情绪。 以下示例为两个文档打分，一个是英文文档，另一个是西班牙文文档。

将以下代码添加到[上一部分](#Detect)的代码中。

```golang
    // Detect sentiment.
    fmt.Println("===== DETECT SENTIMENT ======")

    uri = uriBase + uriPath + "sentiment"

    data = []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err = json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r = strings.NewReader("{\"documents\": " + string(documents) + "}")

    client = &http.Client{
        Timeout: time.Second * 2,
    }

    req, err = http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err = client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err = ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    json.Unmarshal(body, &f)

    jsonFormatted, err = json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
```

## <a name="analyze-sentiment-response"></a>分析情绪响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases-request"></a>提取关键短语请求

关键短语提取 API 使用[关键短语方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)从文本文档中提取关键短语。 以下示例为英文和西班牙文文档提取关键短语。

将以下代码添加到[上一部分](#SentimentAnalysis)的代码中。

```golang
    // Extract key phrases.
    fmt.Println("===== EXTRACT KEY PHRASES ======")

    uri = uriBase + uriPath + "keyPhrases"

    data = []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err = json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r = strings.NewReader("{\"documents\": " + string(documents) + "}")

    client = &http.Client{
        Timeout: time.Second * 2,
    }

    req, err = http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err = client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err = ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    json.Unmarshal(body, &f)

    jsonFormatted, err = json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
```

## <a name="extract-key-phrases-response"></a>提取关键短语响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities-request"></a>识别链接的实体请求

实体链接 API 使用[实体链接方法](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634)识别文本文档中的已知实体。 以下示例识别英文文档的实体。

将以下代码添加到[上一部分](#KeyPhraseExtraction)的代码中。

```golang
    // Detect linked entities.
    fmt.Println("===== DETECT LINKED ENTITIES ======")

    uri = uriBase + uriPath + "entities"

    data = []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "en", "text": "The Seattle Seahawks won the Super Bowl in 2014."},
    }

    documents, err = json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r = strings.NewReader("{\"documents\": " + string(documents) + "}")

    client = &http.Client{
        Timeout: time.Second * 2,
    }

    req, err = http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err = client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err = ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    json.Unmarshal(body, &f)

    jsonFormatted, err = json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="identify-linked-entities-response"></a>识别链接的实体响应

在 JSON 中返回成功的响应，如以下示例所示：

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "name": "Xbox One",
                    "matches": [
                        {
                            "text": "XBox One",
                            "offset": 23,
                            "length": 8
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Xbox One",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                    "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                },
                {
                    "name": "4K resolution",
                    "matches": [
                        {
                            "text": "4K",
                            "offset": 63,
                            "length": 2
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "4K resolution",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/4K_resolution",
                    "bingId": "1d4d689e-9cbf-b9eb-6ecf-f3296aaa96d8"
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "name": "Seattle Seahawks",
                    "matches": [
                        {
                            "text": "Seattle Seahawks",
                            "offset": 4,
                            "length": 16
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Seattle Seahawks",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle_Seahawks",
                    "bingId": "1bf844db-8225-f90c-a78e-1787fb8af0ce"
                },
                {
                    "name": "Super Bowl",
                    "matches": [
                        {
                            "text": "Super Bowl",
                            "offset": 29,
                            "length": 10
                        }
                    ],
                    "wikipediaLanguage": "en",
                    "wikipediaId": "Super Bowl",
                    "wikipediaUrl": "https://en.wikipedia.org/wiki/Super_Bowl",
                    "bingId": "ce1fece8-34c4-6249-a1aa-2e779294760e"
                }
            ]
        }
    ],
    "errors": []
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 进行文本分析](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)  
 [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)
