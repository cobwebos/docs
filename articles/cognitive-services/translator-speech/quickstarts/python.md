---
title: 适用于 Azure 认知服务中的 Microsoft 语音翻译 API 的 Python 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上 Microsoft 认知服务中的 Microsoft 语音翻译 API。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: 40535f400bc5359019e89320489721bf481c1210
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39124820"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>将 Microsoft 语音翻译 API 与 Python 配合使用的快速入门 
<a name="HOLTop"></a>

本文演示如何使用 Microsoft 语音翻译 API 翻译 .wav 文件中的口述内容。

## <a name="prerequisites"></a>先决条件

需要使用 [Python 3.x](https://www.python.org/downloads/) 来运行此代码。

需为 Python 安装 [websocket-client 包](https://pypi.python.org/pypi/websocket-client)。

将需要一个名为“speak.wav”的 .wav 文件，该文件与从以下代码编译的可执行文件位于同一文件夹中。 此 .wav 文件应采用标准 PCM、16 位、16 kHz 单声道格式。

必须创建一个具有 Microsoft 语音翻译 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 需要一个来自 [Azure 仪表板](https://portal.azure.com/#create/Microsoft.CognitiveServices)的付费订阅密钥。

## <a name="translate-speech"></a>翻译语音

下面的代码将语音从一种语言翻译为另一种语言。

1. 在喜欢使用的 IDE 中创建新的 Python 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
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
