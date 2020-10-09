---
title: 快速入门：使用 REST API 和 Ruby 检查拼写 - 必应拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 和 Ruby 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 0b29ba1b09123784bfbac6fda4f5a1c6953f4e49
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327385"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>快速入门：使用必应拼写检查 REST API 和 Ruby 检查拼写

根据此快速入门中的说明对使用 Ruby 的必应拼写检查 REST API 进行第一次调用。 此简单的应用程序将向 API 发送请求并返回一系列建议的更正。 

虽然此应用程序是使用 Ruby 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) 上提供了此应用程序的源代码

## <a name="prerequisites"></a>先决条件

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 或更高版本。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢的编辑器或 IDE 中创建新的 Ruby 文件，并添加以下要求： 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. 为订阅密钥、终结点 URI 和路径创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。 创建请求参数：

   1. 用 `=` 运算符将市场代码分配到 `mkt` 参数。 市场代码指示发出请求的国家/地区的代码。 

   1. 使用 `&` 运算符添加 `mode` 参数，然后分配拼写检查模式。 模式可以是 `proof`（捕获大部分拼写/语法错误）或 `spell`（捕获大部分拼写错误，但是捕获的语法错误较少）。 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>发送拼写检查请求

1. 从主机 uri、路径和参数字符串创建 URI。 将查询设置为包含要进行拼写检查的文本。

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. 使用之前构造的 URI 创建请求。 将密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. 发送请求。

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. 获取 JSON 响应，并将其打印到控制台。 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>运行应用程序

生成并运行项目。 如果使用命令行，则使用以下命令运行应用程序：

   ```bash
   ruby <FILE_NAME>.rb
   ```

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorials/spellcheck.md)

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
