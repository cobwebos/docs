---
title: 快速入门：将语音合成音频文件，C# (.NET) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 17e5f35e83f5cb6e8115a9d344f93abf84e5b38a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156637"
---
## <a name="prerequisites"></a>必备条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

第一步是确保在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `Program.cs`。

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手

添加一些代码作为项目的框架。 请注意，已创建名为 `SynthesisToAudioFileAsync()` 的异步方法。

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `SpeechSynthesizer` 对象之前，需要创建一个使用订阅密钥和订阅区域的配置。 将此代码插入 `SynthesisToAudioFileAsync()` 方法。

````C#
// Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
// from here: https://aka.ms/speech/sdkregion).
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>创建音频配置

现在，需要创建指向音频文件的 ````AudioConfig```` 对象。 此对象是在 using 语句中创建的，以确保正确释放非托管资源。 将此代码插入语音配置下的 `SynthesisToAudioFileAsync()` 方法。

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>初始化 SpeechSynthesizer

现在，使用之前创建的 `SpeechConfig` 和 `AudioConfig` 对象创建 `SpeechSynthesizer` 对象。 此对象也是在 using 语句中创建的，以确保正确释放非托管资源。 在用于包装 ````AudioConfig```` 对象的 using 语句中，将此代码插入 `SynthesisToAudioFileAsync()` 方法中。

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>使用 SpeakTextAsync 的合成文本

在 `SpeechSynthesizer` 对象中，我们将调用 `SpeakTextAsync()` 方法。 此方法将文本发送到语音服务，该服务会将其转换为音频。 如果未显式指定 `config.VoiceName`，则 `SpeechSynthesizer` 将使用默认语音。

在 using 语句中，添加以下代码：
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>检查是否有错误

当语音服务返回合成结果时，应进行检查以确保文本已成功合成。

在 using 语句中的 `SpeakTextAsync()` 下方，添加以下代码：
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>查看代码

此时，代码应如下所示：

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，可以使用语音服务构建应用并测试语音合成。

1. “编译代码”- 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”    。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。
3. 开始合成 - 将文本转换为语音，并保存在指定的音频数据中  。

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另请参阅

- [创建自定义语音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
