---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: edde734409d064f1bff0212a1cbaecf136206772
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586831"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用喜欢的 IDE 或编辑器创建新的 Go 项目，或在桌面上创建新的文件夹。 然后，将此代码片段复制到项目/文件夹的 `get-languages.go` 文件中。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>创建 main 函数

让我们为应用程序创建 main 函数。 你会注意到，它是单行代码。 这是因为，我们要创建的单一函数用于为翻译获取和输出受支持语言的列表。

此示例将尝试从环境变量 `TRANSLATOR_TEXT_ENDPOINT` 读取“翻译”终结点。 如果不熟悉环境变量，则可将 `endpoint` 设置为字符串并注释掉条件语句。

将以下代码复制到项目中：

```go
func main() {
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/languages?api-version=3.0"
    getLanguages(uri)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>创建获取受支持语言列表的函数

我们来创建获取受支持语言列表的函数。

```go
func getLanguages(uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

接下来，构造 URL。 使用 `Parse()` 和 `Query()` 方法生成 URL。

将此代码复制到 `getLanguages` 函数中。

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> 有关终结点、路由和请求参数的详细信息，请参阅[翻译 3.0：语言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

## <a name="build-the-request"></a>生成请求

将请求正文编码为 JSON 后，可以生成 POST 请求并调用“翻译”。

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

如果使用的是认知服务多服务订阅，则还必须在请求参数中包括 `Ocp-Apim-Subscription-Region`。 [详细了解如何使用多服务订阅进行身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="handle-and-print-the-response"></a>处理并输出响应

将此代码添加到 `getLanguages` 函数以解码 JSON 响应，然后格式化并输出结果。

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>将其放在一起

就是这样，你已构建了一个简单的程序。该程序可以调用“翻译”并返回 JSON 响应。 现在，可以运行该程序了：

```console
go run get-languages.go
```

如果希望将你的代码与我们的进行比较，请查看 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go) 上提供的完整示例。

## <a name="sample-response"></a>示例响应

请在此[语言列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中查找国家/地区缩写。

成功的响应以 JSON 格式返回，如以下示例所示：

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>后续步骤

查看 API 参考，了解使用“翻译”可以执行的所有操作。

> [!div class="nextstepaction"]
> [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
