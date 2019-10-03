---
title: 转录与语音 SDK 语音服务的多参与者对话
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 使用对话脚本。 适用于C++、 C#和 Java。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066429"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>通过语音 SDK 转录多参与者对话

通过语音 SDK 的**ConversationTranscriber** API, 你可以通过使用`PullStream`或`PushStream`将音频流式传输到语音服务, 使用添加、删除和标识参与者的能力转录会议/对话。

## <a name="limitations"></a>限制

* Windows、Linux 和 Android C++上C#的、和 Java 支持会话 transcriber。
* ROOBO DevKit 是支持用于创建会话转录的硬件环境, 因为它提供了环形多麦克风阵列, 可以有效地利用该阵列来识别演讲者。 [有关详细信息, 请参阅语音设备 SDK](speech-devices-sdk.md)。
* 对会话脚本的语音 SDK 支持仅限于带有8个通道的16位 16 kHz PCM 音频的音频拉取和推送模式流。 目前, 仅支持将这些工具包用于8声道音频捕获:
   * [ROOBO 智能音频圆形 7-Mic 深色](https://ddk.roobo.com/)
   * [Azure KINECT 深色](https://azure.microsoft.com/en-in/services/kinect-dk/)。
* 会话脚本目前在以下区域的 "en-us" 和 "zh-chs" 语言中提供: centralus 和 eastasia。

## <a name="prerequisites"></a>先决条件

* [了解如何通过语音 SDK 使用语音到文本。](quickstart-csharp-dotnet-windows.md)
* [获取你的语音试用订阅。](https://azure.microsoft.com/try/cognitive-services/)
* 需要语音 SDK 版本1.5.1 或更高版本。

## <a name="create-voice-signatures-for-participants"></a>为参与者创建语音签名

第一步是为会话参与者创建语音签名。 若要高效地进行发言人识别, 需要创建语音签名。

### <a name="requirements-for-input-wave-file"></a>输入波形文件的要求

* 用于创建语音签名的输入音频波形文件应为16位示例、16 kHz 采样率和单通道 (单声道) 格式。
* 每个音频采样的建议长度介于30秒到2分钟之间。

下面的示例演示了使用中C#[的 REST API](https://aka.ms/cts/signaturegenservice)两种不同的方法来创建语音签名:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
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

## <a name="transcribing-conversations"></a>转录对话

若要转录多个参与者的会话, `ConversationTranscriber`请创建`AudioConfig`与为会话会话创建的对象关联的对象, 并使用`PullAudioInputStream`或`PushAudioInputStream`流式传输音频。

假设你有一个名`MyConversationTranscriber`为的 ConversationTranscriber 类。 你的代码可能如下所示:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
