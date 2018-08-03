---
title: 适用于 Azure 认知服务中的 Microsoft 语音翻译 API 的 C# 快速入门 | Microsoft Docs
description: 获取信息和代码示例，有助于快速开始使用 Azure 上 Microsoft 认知服务中的 Microsoft 语音翻译 API。
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 4f12d74aedbcadc311cd9c5ccd12dc1ad3501dbf
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205096"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-c"></a>将 Microsoft 语音翻译 API 与 C# 配合使用的快速入门 
<a name="HOLTop"></a>

本文演示如何使用 Microsoft 语音翻译 API 翻译 .wav 文件中的口述内容。

## <a name="prerequisites"></a>先决条件

需要使用 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 才能在 Windows 上运行此代码。 （免费的社区版也可以。）

将需要一个名为“speak.wav”的 .wav 文件，该文件与从以下代码编译的可执行文件位于同一文件夹中。 此 .wav 文件应采用标准 PCM、16 位、16 kHz 单声道格式。 可使用[文本转语音 API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech) 获取此类 .wav 文件。

必须创建一个具有 Microsoft 语音翻译 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 需要一个来自 [Azure 仪表板](https://portal.azure.com/#create/Microsoft.CognitiveServices)的付费订阅密钥。

## <a name="translate-speech"></a>翻译语音

下面的代码将语音从一种语言翻译为另一种语言。

1. 在喜欢使用的 IDE 中新建一个 C# 项目。
2. 添加下方提供的代码。
3. 使用对订阅有效的访问密钥替换 `key` 值。
4. 运行该程序。

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
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
