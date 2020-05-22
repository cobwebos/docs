---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 649af0d3b1503a5607717c4ccbadd8f1d822be32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586833"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的项目。 然后，将此代码片段复制到项目的名为 `get-languages.js` 的文件中。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request uuidv4`。

若要构造 HTTP 请求以及为 `'X-ClientTraceId'` 标头创建唯一标识符，必须使用这些模块。

## <a name="set-the-endpoint"></a>设置终结点

此示例将尝试从环境变量 `TRANSLATOR_TEXT_ENDPOINT` 读取“翻译”终结点。 如果不熟悉环境变量，则可将 `endpoint` 设置为字符串并注释掉条件语句。

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>配置请求

使用通过请求模块提供的 `request()` 方法，可以以 `options` 对象的形式传递 HTTP 方法、URL、请求参数、标头和 JSON 正文。 在此代码片段中，我们将配置请求：

>[!NOTE]
> 有关终结点、路由和请求参数的详细信息，请参阅[翻译 3.0：语言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

如果使用的是认知服务多服务订阅，则还必须在请求参数中包括 `Ocp-Apim-Subscription-Region`。 [详细了解如何使用多服务订阅进行身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

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

就是这样，你已构建了一个简单的程序。该程序可以调用“翻译”并返回 JSON 响应。 现在，可以运行该程序了：

```console
node get-languages.js
```

如果希望将你的代码与我们的进行比较，请查看 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) 上提供的完整示例。

## <a name="sample-response"></a>示例响应

请在此[语言列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中查找国家/地区缩写。

此示例已截断，显示了结果的片段：

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

## <a name="clean-up-resources"></a>清理资源

如果已将订阅密钥硬编码到程序中，请确保在完成本快速入门后删除该订阅密钥。

## <a name="next-steps"></a>后续步骤

查看 API 参考，了解使用“翻译”可以执行的所有操作。

> [!div class="nextstepaction"]
> [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
