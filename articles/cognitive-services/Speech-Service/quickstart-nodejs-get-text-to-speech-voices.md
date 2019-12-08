---
title: 快速入门：列出“文本转语音”语音，Node.js - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用 Node.js 获取某个区域/终结点的标准语音和神经语音的完整列表。 列表以 JSON 形式返回，语音可用性因区域而异。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bd063102a4c0cbdc4b62df027e643b117b412278
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812856"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>快速入门：使用 Node.js 获取“文本转语音”语音的列表

本快速入门介绍如何使用 Node.js 获取某个区域/终结点的标准语音和神经语音的完整列表。 列表以 JSON 形式返回，语音可用性因区域而异。 有关受支持的区域的列表，请参阅[区域](regions.md)。

此快速入门需要包含语音服务资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](get-started.md)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Node 8.12.x 或更高版本](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥。 [免费获得一个！](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>创建项目并声明需要的依赖项

在你喜欢使用的 IDE 或编辑器中新建一个 Node.js 项目。 然后，将此代码片段复制到项目的名为 `get-voices.js` 的文件中。

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request request-promise`。

## <a name="get-an-access-token"></a>获取访问令牌

文本转语音 REST API 需要使用访问令牌进行身份验证。 若要获取访问令牌，需要进行交换。 此函数通过 `issueToken` 终结点使用语音服务订阅密钥来交换访问令牌。

此示例假定语音服务订阅位于“美国西部”区域。 如果使用其他区域，请更新 `uri` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

将以下代码复制到项目中：

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> 有关身份验证的详细信息，请参阅[使用访问令牌进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

在下一部分中，我们将创建用于获取语音列表的函数，并将 JSON 输出保存到文件。

## <a name="make-a-request-and-save-the-response"></a>发出请求并保存响应

此处我们将要生成请求并保存返回的语音列表。 此示例假设使用“美国西部”终结点。 如果已将资源注册到其他区域，请务必更新 `uri`。 有关详细信息，请参阅[语音服务区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下来，为请求添加所需的标头。 最后，向服务发出请求。 如果请求成功，并返回 200 状态代码，则响应将写入文件。

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>将其放在一起

即将完成。 最后一步是创建异步函数。 此函数将从环境变量读取订阅密钥、获取令牌、等待请求完成，然后将 JSON 响应写入到文件。

如果不熟悉环境变量，或希望使用硬编码为字符串的订阅密钥来测试，请将 `process.env.SPEECH_SERVICE_KEY` 替换为字符串形式的订阅密钥。

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，就可以运行示例应用了。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>清理资源

请务必删除示例应用的源代码中的机密信息，例如订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Node.js 示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>另请参阅

* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)
