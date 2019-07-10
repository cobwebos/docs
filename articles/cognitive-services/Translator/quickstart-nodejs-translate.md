---
title: 快速入门：翻译文本，Node.js - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用文本翻译 API 和 Node.js 将文本从一种语言翻译成另一种语言。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 2a314a7a12635319bb2f688a2aa12d78e9f360af
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444993"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-with-nodejs"></a>快速入门：使用 Node.js 通过文本翻译 API 来翻译字符串

本快速入门介绍如何使用 Node.js 和文本翻译 REST API 将文本字符串从英文翻译为意大利文和德文。

此快速入门需要包含文本翻译资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Node 8.12.x 或更高版本](https://nodejs.org/en/)
* 适用于文本翻译的 Azure 订阅密钥

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用你喜欢的 IDE 或编辑器创建一个新项目，或者在桌面上创建一个包含名为 `translate-text.js` 的文件的新文件夹。 然后将以下代码片段复制到项目/文件中：

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request uuidv4`。

若要构造 HTTP 请求以及为 `'X-ClientTraceId'` 标头创建唯一标识符，必须使用这些模块。

## <a name="set-the-subscription-key"></a>设置订阅密钥

此代码会尝试从环境变量 `TRANSLATOR_TEXT_KEY` 读取文本翻译订阅密钥。 如果不熟悉环境变量，则可将 `subscriptionKey` 设置为字符串并注释掉条件语句。

将以下代码复制到项目中：

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>配置请求

使用通过请求模块提供的 `request()` 方法，可以以 `options` 对象的形式传递 HTTP 方法、URL、请求参数、标头和 JSON 正文。 在此代码片段中，我们将配置请求：

>[!NOTE]
> 有关终结点、路由和请求参数的详细信息，请参阅[文本翻译 API 3.0：翻译](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate)。

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': ['de', 'it']
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

若要对请求进行身份验证，最容易的方法是将订阅密钥作为 `Ocp-Apim-Subscription-Key` 标头传入，这是我们在此示例中使用的方法。 替代方法是交换订阅密钥来获取访问令牌，将访问令牌作为 `Authorization` 标头传入，以便对请求进行验证。 

如果使用的是认知服务多服务订阅，则还必须在请求标头中包括 `Ocp-Apim-Subscription-Region`。 

有关详细信息，请参阅[身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="make-the-request-and-print-the-response"></a>发出请求并输出响应

接下来，请使用 `request()` 方法来创建请求。 它采用我们在上一部分创建的 `options` 对象作为第一个参数，然后输出美化的 JSON 响应。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> 在此示例中，我们将在 `options` 对象中定义 HTTP 请求。 不过，请求模块也支持便捷方法，例如 `.post` 和 `.get`。 有关详细信息，请参阅[便捷方法](https://github.com/request/request#convenience-methods)。

## <a name="put-it-all-together"></a>将其放在一起

就是这样，你已构建了一个简单的程序。该程序可以调用文本翻译 API 并返回 JSON 响应。 现在，可以运行该程序了：

```console
node translate-text.js
```

如果希望将你的代码与我们的进行比较，请查看 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) 上提供的完整示例。

## <a name="sample-response"></a>示例响应

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

## <a name="clean-up-resources"></a>清理资源

如果已将订阅密钥硬编码到程序中，请确保在完成本快速入门后删除该订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Node.js 示例](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS)

## <a name="see-also"></a>另请参阅

除了语言检测，还请了解如何使用文本翻译 API 执行以下操作：

* [直译文本](quickstart-nodejs-transliterate.md)
* [按输入确定语言](quickstart-nodejs-detect.md)
* [获取备用翻译](quickstart-nodejs-dictionary.md)
* [获取支持的语言的列表](quickstart-nodejs-languages.md)
* [根据输入确定句子长度](quickstart-nodejs-sentences.md)
