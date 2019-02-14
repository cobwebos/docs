---
title: 快速入门：获取受支持的语言，Ruby - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用文本翻译 API 和 Ruby 获取翻译、音译和字典查找支持的语言列表以及示例。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: erhopf
ms.openlocfilehash: 1b92adcf528a1ccd00983e6c0dd952fefab5dd7d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2019
ms.locfileid: "55975460"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-ruby"></a>快速入门：通过文本翻译 REST API (Ruby) 获取受支持的语言

在该快速入门中，你将使用文本翻译 API 获取翻译、音译和字典查找支持的语言列表以及示例。

## <a name="prerequisites"></a>先决条件

需要使用 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 或更高版本来运行此代码。

## <a name="languages-request"></a>语言请求

以下代码使用 [Languages](./reference/v3-0-languages.md) 方法获取翻译、音译、查询字典支持的语言列表和示例。

1. 在你喜欢使用的代码编辑器中新建一个 Ruby 项目。
2. 添加以下提供的代码。
3. 运行该程序。

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

# Write response to file
File.open(output_path, 'w' ) do |output|
    output.print json
end
```

## <a name="languages-response"></a>语言响应

成功的响应以 JSON 格式返回，如以下示例所示：

```json
{
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
  },
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
  }
}
```

## <a name="next-steps"></a>后续步骤

浏览此快速入门的示例代码和其他内容，包括翻译和音译，以及 GitHub 上的其他示例文本翻译项目。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Ruby 示例](https://aka.ms/TranslatorGitHub?type=&language=ruby)
