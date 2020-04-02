---
title: 实时对话转录（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何将实时对话转录与语音 SDK 一起使用。 适用于 C++、C# 和 Java。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520915"
---
# <a name="real-time-conversation-transcription-preview"></a>实时对话转录（预览）

语音 SDK 的 **"对话转录器"API**允许您转录会议和其他对话，以便使用 或`PullStream``PushStream`将音频流式传输到语音服务，从而添加、删除和识别多个参与者。 本主题要求您了解如何将语音到文本与语音 SDK（版本 1.8.0 或更高版本）一起使用。 有关详细信息，请参阅[什么是语音服务](overview.md)。

## <a name="limitations"></a>限制

- 在 Windows、Linux 和 Android 上支持 C++、C# 和 Java 的会话转录器 API。
- 目前在以下区域提供"en-US"和"zh-CN"语言：_中亚_和_东亚_。
- 需要一个带播放参考流的 7 麦克风圆形多麦克风阵列。 麦克风阵列应符合[我们的规格](https://aka.ms/sdsdk-microphone)。
- [语音设备 SDK](speech-devices-sdk.md)提供合适的设备和演示对话转录的示例应用。

## <a name="optional-sample-code-resources"></a>可选的示例代码资源

语音设备 SDK 在 Java 中提供示例代码，以便使用 8 个通道进行实时音频捕获。

- [ROOBO 设备示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Azure Kinect 开发人员工具包示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>先决条件

语音服务订阅。 如果没有["语音试用版"订阅，则可以获取语音试用版订阅](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="create-voice-signatures"></a>创建语音签名

第一步是为对话参与者创建语音签名，以便有效地识别演讲者。

### <a name="audio-input-requirements"></a>音频输入要求

- 用于创建语音签名的输入音频波文件应采用 16 位采样、16 kHz 采样速率和单声道（单声道）格式。
- 每个音频样本的建议长度在三十秒到两分钟之间。

### <a name="sample-code"></a>示例代码

下面的示例显示了使用 C# 中的 REST [API](https://aka.ms/cts/signaturegenservice)创建语音签名的两种不同方法。 请注意，您需要将真实信息替换为"您的订阅密钥"，您的波文件名称为"扬声器Voice.wav"，以及您所在区域`{region}`和"您的服务区域"（_中央_或_东亚_）。

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>转录对话

以下示例代码演示如何实时转录三个扬声器的对话。 它假定您已经为每个扬声器创建了语音签名，如上所示。 创建语音配置对象时，将真实信息替换为"订阅密钥"和"您的服务区域"。

示例代码亮点包括：

- 使用使用`Conversation`使用会议标识符`SpeechConfig`生成的会议标识符从对象创建对象`Guid.NewGuid()`
- 创建`ConversationTranscriber`对象并加入对话以`JoinConversationAsync()`开始转录
- 注册感兴趣的事件
- 使用对话对象向对话添加或删除参与者
- 流式传输音频
- 在语音 SDK 版本 1.9.0`int`中`string`，语音签名版本字段中支持和值类型。

转录和扬声器标识符在已注册的事件中返回。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [异步对话听录](how-to-async-conversation-transcription.md)
