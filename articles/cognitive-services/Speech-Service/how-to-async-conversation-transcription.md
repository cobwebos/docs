---
title: 异步对话脚本（预览版）-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音服务的异步对话脚本。 仅适用于 Java。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: d20cdb2f37c3da357ca112045a0d2845bbb6df98
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260015"
---
# <a name="asynchronous-conversation-transcription-preview"></a>异步对话脚本（预览）

在本文中，使用**RemoteConversationTranscriptionClient** API 演示了异步对话脚本。 如果已将会话脚本配置为执行异步脚本并具有 `conversationId`，则可以使用**RemoteConversationTranscriptionClient** API 获取与 `conversationId` 关联的脚本。

## <a name="asynchronous-vs-real-time--asynchronous"></a>异步与实时 + 异步

对于异步脚本，会流式传输会话音频，但不需要实时返回脚本。 相反，发送音频后，使用 `Conversation` 的 `conversationId` 来查询异步脚本的状态。 异步脚本准备就绪后，你将获得 `RemoteConversationTranscriptionResult`。

通过实时和异步，你可以实时地获取脚本，但也可以通过使用 `conversationId` 进行查询来获得脚本（类似于异步方案）。

完成异步脚本需要执行两个步骤。 第一步是上传音频，只选择 "异步" 或 "实时" 和 "异步"。 第二步是获取脚本结果。

## <a name="upload-the-audio"></a>上传音频

在可以执行异步脚本之前，需要使用 Microsoft 认知语音客户端 SDK （版本1.8.0 或更高版本）将音频发送到会话脚本服务。

此示例代码演示如何为仅限异步模式创建会话 transcriber。 若要将音频流式传输到 transcriber，需要[通过语音 SDK 实时添加从转录对话](how-to-use-conversation-transcription-service.md)派生的音频流代码。 请参阅该主题的 "**限制**" 部分，查看支持的平台和语言 api。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

如果需要实时_加上_异步，注释和取消注释相应的代码行，如下所示：

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>获取脚本结果

此步骤将获取异步脚本结果，但假设你可能需要的任何实时处理都在其他位置完成。 有关详细信息，请参阅[通过语音 SDK 实时转录对话](how-to-use-conversation-transcription-service.md)。

对于此处所示的代码，你需要**远程会话版本 1.8.0**，该版本仅适用于 Windows、Linux 和 Android 上的 Java （1.8.0 或更高版本）（仅限 API 级别26或更高版本）。

### <a name="obtaining-the-client-sdk"></a>获取客户端 SDK

可以通过按如下所示编辑 pom .xml 文件来获取**远程对话**。

1. 在文件末尾，在结束标记 `</project>`之前，请创建一个 `repositories` 元素，其中包含对语音 SDK 的 Maven 存储库的引用：

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 另外添加一个 `dependencies` 元素，并将 remoteconversation 1.8.0 作为依赖项：

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. 保存更改

### <a name="sample-transcription-code"></a>示例脚本代码

`conversationId`后，在客户端应用程序中创建远程对话脚本客户端**RemoteConversationTranscriptionClient** ，以查询异步脚本的状态。 使用**RemoteConversationTranscriptionClient**中的**GetTranscriptionOperation**方法获取[PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java)对象。 PollerFlux 对象将包含有关远程操作状态**RemoteConversationTranscriptionOperation**和最终结果**RemoteConversationTranscriptionResult**的信息。 操作完成后，通过对[SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)调用**GetFinalResult**来获取**RemoteConversationTranscriptionResult** 。 在此代码中，我们只是将结果内容打印到系统输出。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
