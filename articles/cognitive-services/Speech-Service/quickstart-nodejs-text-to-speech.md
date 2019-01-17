---
title: 快速入门：将文本转换为语音，Node.js - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍了如何使用 Node.js 和文本转语音 REST API 将文本转换为语音。 本指南中包含的示例文本以语音合成标记语言 (SSML) 的形式构造。 这样你就可以选择语音响应的语音和语言。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 8cc676355f432a25550b337e5de747c4956d3142
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358875"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>快速入门：使用 Node.js 将文本转换为语音

本快速入门介绍了如何使用 Node.js 和文本转语音 REST API 将文本转换为语音。 本指南中包含的请求正文以[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 的形式构造，这样你就可以选择响应的语音和语言。

此快速入门需要包含语音服务资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](get-started.md)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Node 8.12.x 或更高版本](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥。 [免费获得一个！](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>创建项目并声明需要的依赖项

在你喜欢使用的 IDE 或编辑器中新建一个 Node.js 项目。 然后，将此代码片段复制到项目的名为 `tts.js` 的文件中。

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request readline-sync`。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>设置订阅密钥并为 TTS 创建提示

在以下几个部分中，我们将创建一些函数来用于处理授权、调用文本转语音 API，以及验证响应。 首先，让我们添加一个订阅密钥并创建用于文本输入的提示。

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>获取访问令牌

文本转语音 REST API 需要使用访问令牌进行身份验证。 若要获取访问令牌，需要进行交换。 此示例通过 `issueToken` 终结点使用语音服务订阅密钥来交换访问令牌。

此函数采用两个参数：你的语音服务订阅密钥，以及一个回调函数。 在函数获取访问令牌后，它将该值传递给回调函数。 在下一部分中，我们将创建函数来调用文本转语音 API 并保存合成的语音响应。

此示例假定语音服务订阅位于“美国西部”区域。 如果使用其他区域，请更新 `uri` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

将以下代码复制到项目中：

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> 有关身份验证的详细信息，请参阅[使用访问令牌进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="make-a-request-and-save-the-response"></a>发出请求并保存响应

此处，我们将生成对文本转语音 API 的请求并保存语音响应。 此示例假设使用“美国西部”终结点。 如果已将资源注册到其他区域，请务必更新 `uri`。 有关详细信息，请参阅[语音服务区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下来，需要为请求添加所需的标头。 请务必使用资源的名称（在 Azure 门户中可以找到）更新 `User-Agent`，并将 `X-Microsoft-OutputFormat` 设置为首选的音频输出。 如需输出格式的完整列表，请参阅[音频输出](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs)。

然后，使用语音合成标记语言 (SSML) 构造请求正文。 此示例将定义结构，并使用前面创建的 `text` 输入。

>[!NOTE]
> 此示例使用 `JessaRUS` 语音字体。 如需 Microsoft 提供的语音/语言的完整列表，请参阅[语言支持](language-support.md)。
> 若要为品牌创建唯一且可识别的语音，请参阅[创建自定义语音字体](how-to-customize-voice-font.md)。

最后，向服务发出请求。 如果该请求成功，则会返回 200 状态代码，语音响应将写入为 `sample.wav`。

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
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
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>将其放在一起

即将完成。 最后一步是调用 `textToSpeech` 函数。

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
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

* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)
