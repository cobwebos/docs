---
title: 快速入门：将文本转换为语音，Node.js - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍了如何使用 Node.js 和文本转语音 REST API 将文本转换为语音。 本指南中包含的示例文本以语音合成标记语言 (SSML) 的形式构造。 这样你就可以选择语音响应的语音和语言。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: a7713576565ca2632d7d91857040ece4d02c411b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621941"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>快速入门：使用 Node.js 将文本转换为语音

本快速入门介绍了如何使用 Node.js 和文本转语音 REST API 将文本转换为语音。 本指南中包含的请求正文以[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 的形式构造，这样你就可以选择响应的语音和语言。

本快速入门需要[Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)与语音服务资源。 如果没有帐户，可以使用[免费试用版](get-started.md)获取订阅密钥。

## <a name="prerequisites"></a>必备组件

本快速入门需要：

* [Node 8.12.x 或更高版本](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥。 [免费获得一个！](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>创建项目并声明需要的依赖项

在你喜欢使用的 IDE 或编辑器中新建一个 Node.js 项目。 然后，将此代码片段复制到项目的名为 `tts.js` 的文件中。

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request request-promise xmlbuilder readline-sync`。

## <a name="get-an-access-token"></a>获取访问令牌

文本转语音 REST API 需要使用访问令牌进行身份验证。 若要获取访问令牌，需要进行交换。 此函数将交换访问令牌使用语音服务的订阅密钥`issueToken`终结点。

此示例假定您的语音服务的订阅已在美国西部区域中。 如果使用其他区域，请更新 `uri` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

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

在下一部分中，我们将创建函数来调用文本转语音 API 并保存合成的语音响应。

## <a name="make-a-request-and-save-the-response"></a>发出请求并保存响应

此处，我们将生成对文本转语音 API 的请求并保存语音响应。 此示例假设使用“美国西部”终结点。 如果已将资源注册到其他区域，请务必更新 `uri`。 有关详细信息，请参阅[语音服务区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下来，需要为请求添加所需的标头。 请务必使用资源的名称（在 Azure 门户中可以找到）更新 `User-Agent`，并将 `X-Microsoft-OutputFormat` 设置为首选的音频输出。 如需输出格式的完整列表，请参阅[音频输出](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

然后，使用语音合成标记语言 (SSML) 构造请求正文。 此示例将定义结构，并使用前面创建的 `text` 输入。

>[!NOTE]
> 此示例使用 `JessaRUS` 语音字体。 如需 Microsoft 提供的语音/语言的完整列表，请参阅[语言支持](language-support.md)。
> 若要为品牌创建唯一且可识别的语音，请参阅[创建自定义语音字体](how-to-customize-voice-font.md)。

最后，向服务发出请求。 如果该请求成功，则会返回 200 状态代码，语音响应将写入为 `TTSOutput.wav`。

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>将其放在一起

即将完成。 最后一步是创建异步函数。 此函数会从环境变量中读取订阅密钥，提示你输入文本，接着获取令牌，等待请求完成，然后将文本转换为语音，再将音频另存为 .wav。

如果不熟悉环境变量，或者首选在测试时将订阅密钥硬编码为字符串，请将 `process.env.SPEECH_SERVICE_KEY` 替换为字符串形式的订阅密钥。

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，可以运行文本转语音示例应用。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
node tts.js
```

出现提示时，键入要转换为语音的任何文本内容。 如果转换成功，则项目文件夹中会出现语音文件。 使用偏好的媒体播放器播放该文件。

## <a name="clean-up-resources"></a>清理资源

请务必删除示例应用的源代码中的机密信息，例如订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Node.js 示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>另请参阅

* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)
