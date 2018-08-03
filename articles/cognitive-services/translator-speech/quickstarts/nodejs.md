---
title: 适用于 Azure 认知服务中的 Microsoft 语音翻译 API 的 Node.js 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上 Microsoft 认知服务中的 Microsoft 语音翻译 API。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 0c0f3120811bba164a07783bc7ce3b7af389fd2b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205208"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>将 Microsoft 语音翻译 API 与 Node.js 配合使用的快速入门 
<a name="HOLTop"></a>

本文演示如何使用 Microsoft 语音翻译 API 翻译 .wav 文件中的口述内容。

## <a name="prerequisites"></a>先决条件

需要使用 [Node.js 6](https://nodejs.org/en/download/) 来运行此代码。

需为 Node.js 安装 [Websocket 包](https://www.npmjs.com/package/websocket)。

需要一个名为“speak.wav”的 .wav 文件，该文件与从以下代码编译的可执行文件位于同一文件夹中。 此 .wav 文件应采用标准 PCM、16 位、16 kHz 单声道格式。 可使用[文本转语音 API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech) 获取此类 .wav 文件。

必须创建一个具有 Microsoft 语音翻译 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 需要一个来自 [Azure 仪表板](https://portal.azure.com/#create/Microsoft.CognitiveServices)的付费订阅密钥。

## <a name="translate-speech"></a>翻译语音

下面的代码将语音从一种语言翻译为另一种语言。

1. 在喜欢使用的 IDE 中新建一个 Node.js 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**翻译语音响应**

成功的结果是创建了名为“speak2.wav”的文件。 该文件包含“speak.wav”中口述内容的翻译。

[返回页首](#HOLTop)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音翻译教程](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>另请参阅 

[语音翻译概述](../overview.md)
[API 参考](http://docs.microsofttranslator.com/speech-translate.html)
