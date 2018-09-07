---
title: 文本翻译使用 Ruby 获取支持的语言 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 在本快速入门中，你将获得翻译、音译和查询字典支持的语言列表以及在认知服务中将文本翻译 API 与 Ruby 配合使用的示例。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 1080d79f6dddfd57816989b7d1c4f95348493ad6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "43768696"
---
# <a name="quickstart-get-supported-languages-with-ruby"></a>快速入门：使用 Ruby 获取支持的语言

在本快速入门中，你将获得翻译、音译和查询字典支持的语言列表以及使用文本翻译 API 的示例。

## <a name="prerequisites"></a>先决条件

需要使用 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 或更高版本来运行此代码。

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="languages-request"></a>Languages 请求

以下代码使用 [Languages](./reference/v3-0-languages.md) 方法获取翻译、音译、查询字典支持的语言列表和示例。

1. 在喜欢使用的代码编辑器中新建一个 Ruby 项目。
2. 添加以下提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = key

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

File.open(output_path, 'w' ) do |output|
    output.print json
end
```

## <a name="languages-response"></a>Languages 响应

在 JSON 中返回成功的响应，如以下示例所示：

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
