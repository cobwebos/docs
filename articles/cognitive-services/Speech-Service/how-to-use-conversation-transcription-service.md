---
title: 实时对话脚本（预览）-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 使用实时对话。 适用于 C++、C# 和 Java。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 4ee597f96d28b43b9c69d1515634aadde2f0ab7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464436"
---
# <a name="real-time-conversation-transcription-preview"></a>实时对话脚本（预览）

通过语音 SDK 的**ConversationTranscriber** API，你可以通过使用 `PullStream` 或 `PushStream`将音频流式传输到语音服务，来转录会议和其他会话，并使其能够添加、删除和标识多个参与者。 本主题要求你了解如何在 Speech SDK （版本1.8.0 或更高版本）中使用语音到文本。 有关详细信息，请参阅[什么是语音服务](overview.md)。

## <a name="limitations"></a>限制

- Windows、Linux 和 Android 上C++的C#、和 Java 支持 ConversationTranscriber API。
- 目前在以下区域的 "en-us" 和 "zh-chs" 语言中提供： _centralus_和_eastasia_。
- 需要一个带有播放引用流的7个 mic 循环多麦克风阵列。 麦克风阵列应满足[我们的规范](https://aka.ms/sdsdk-microphone)。
- [语音设备 SDK](speech-devices-sdk.md)提供了合适的设备和演示会话脚本的示例应用。

## <a name="optional-sample-code-resources"></a>可选的示例代码资源

语音设备 SDK 使用8个通道在 Java 中提供了用于实时音频捕获的示例代码。

- [ROOBO 设备示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Azure Kinect 开发工具包示例代码](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>必备组件

语音服务订阅。 如果你没有[语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)，则可以获取它。

## <a name="create-voice-signatures"></a>创建语音签名

第一步是为会话参与者创建语音签名，以便进行有效的发言人标识。

### <a name="audio-input-requirements"></a>音频输入要求

- 用于创建语音签名的输入音频波形文件应为16位示例、16 kHz 采样率和单通道（单声道）格式。
- 每个音频采样的建议长度介于30秒到2分钟之间。

### <a name="sample-code"></a>代码示例

下面的示例演示了使用中C#[的 REST API](https://aka.ms/cts/signaturegenservice)创建语音签名的两种不同方法。 请注意，您需要将 "YourSubscriptionKey" 的实际信息、"speakerVoice" 的波形文件名和 "YourServiceRegion" （_centralus_或_eastasia_）的 `{region}` 区域替换为 ""。

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
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
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
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
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

下面的示例代码演示如何为三个扬声器实时转录对话。 假设已为每个扬声器创建了语音签名，如上所示。 创建 SpeechConfig 对象时，请将 "YourSubscriptionKey" 和 "YourServiceRegion" 替换为实际信息。

示例代码重点包括：

- 使用使用 `Guid.NewGuid()` 生成的会议标识符从 `SpeechConfig` 对象创建 `Conversation` 对象
- 创建 `ConversationTranscriber` 对象并将会话加入 `JoinConversationAsync()` 以开始脚本
- 注册相关事件
- 使用会话对象在会话中添加或删除参与者
- 流式传输音频

脚本和发言人标识符会返回到已注册的事件中。

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
            using (var conversation = new Conversation(config, meetingId))
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
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

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
> [异步会话脚本](how-to-async-conversation-transcription.md)
