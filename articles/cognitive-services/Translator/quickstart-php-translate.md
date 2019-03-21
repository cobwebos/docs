---
title: 快速入门：翻译文本，PHP - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用文本翻译 API 和 PHP 将文本从一种语言翻译成另一种语言。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: erhopf
ms.openlocfilehash: f8c5f3ae48d0695f19699a5dfd1c956492bb128d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534695"
---
# <a name="quickstart-translate-text-with-the-translator-text-rest-api-php"></a>快速入门：使用文本翻译 REST API (PHP) 对文本进行翻译

在本快速入门中，你将使用文本翻译 API 将文本从一种语言翻译成另一种语言。

## <a name="prerequisites"></a>先决条件

需要 [PHP 5.6.x](https://php.net/downloads.php) 运行此代码。

若要使用文本翻译 API，还需要订阅密钥；请参阅[如何注册文本翻译 API](translator-text-how-to-signup.md)。

## <a name="translate-request"></a>翻译请求

以下代码使用 [Translate](./reference/v3-0-translate.md) 方法将源文本从一种语言翻译为另一种语言。

1. 在你喜欢使用的代码编辑器中新建一个 PHP 项目。
2. 添加以下提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$key = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsofttranslator.com";
$path = "/translate?api-version=3.0";

// Translate to German and Italian.
$params = "&to=de&to=it";

$text = "Hello, world!";

if (!function_exists('com_create_guid')) {
  function com_create_guid() {
    return sprintf( '%04x%04x-%04x-%04x-%04x-%04x%04x%04x',
        mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff ),
        mt_rand( 0, 0xffff ),
        mt_rand( 0, 0x0fff ) | 0x4000,
        mt_rand( 0, 0x3fff ) | 0x8000,
        mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff ), mt_rand( 0, 0xffff )
    );
  }
}

function Translate ($host, $path, $key, $params, $content) {

    $headers = "Content-type: application/json\r\n" .
        "Content-length: " . strlen($content) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n" .
        "X-ClientTraceId: " . com_create_guid() . "\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $content
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$requestBody = array (
    array (
        'Text' => $text,
    ),
);
$content = json_encode($requestBody);

$result = Translate ($host, $path, $key, $params, $content);

// Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
// We want to avoid escaping any Unicode characters that result contains. See:
// https://php.net/manual/en/function.json-encode.php
$json = json_encode(json_decode($result), JSON_UNESCAPED_UNICODE | JSON_PRETTY_PRINT);
echo $json;
?>
```

## <a name="translate-response"></a>翻译响应

成功的响应以 JSON 格式返回，如以下示例所示：

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤

浏览此快速入门的示例代码和其他内容，包括音译和语言识别，以及 GitHub 上的其他示例文本翻译项目。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 PHP 示例](https://aka.ms/TranslatorGitHub?type=&language=php)
