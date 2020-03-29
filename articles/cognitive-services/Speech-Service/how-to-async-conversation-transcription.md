---
title: 异步对话转录（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音服务使用异步对话转录。 仅适用于 Java。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366608"
---
# <a name="asynchronous-conversation-transcription-preview"></a>异步对话转录（预览）

在本文中，使用**远程对话转录客户端**API 演示异步对话转录。 如果已配置对话转录以执行异步转录，并且具有`conversationId`，则可以使用`conversationId`**远程对话转录客户端**API 获取与此关联的转录。

## <a name="asynchronous-vs-real-time--asynchronous"></a>异步与实时 + 异步

使用异步转录，您可以流式传输对话音频，但不需要实时返回转录。 相反，在发送音频后，使用 查询`conversationId``Conversation`的异步转录的状态。 当异步转录准备就绪时，您将获得一个`RemoteConversationTranscriptionResult`。

使用实时加上异步，您可以实时获取转录，但也通过查询`conversationId`（类似于异步方案）获得转录。

完成异步转录需要两个步骤。 第一步是上传音频，选择仅异步或实时加异步。 第二步是获取转录结果。

## <a name="upload-the-audio"></a>上传音频

在可以执行异步转录之前，您需要使用 Microsoft 认知语音客户端 SDK（版本 1.8.0 或以上）将音频发送到对话转录服务。

此示例代码演示如何为仅异步模式创建对话转录器。 为了将音频流式传输到转录器，您需要[使用语音 SDK 实时添加从转录对话](how-to-use-conversation-transcription-service.md)派生的音频流代码。 请参阅该主题的 **"限制"** 部分，查看支持的平台和语言 API。

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
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
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

如果需要实时_加上_异步，请注释和取消注释相应的代码行，如下所示：

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>获取转录结果

此步骤获取异步转录结果，但假定可能需要的任何实时处理都已完成其他位置。 有关详细信息，请参阅[使用语音 SDK 实时转录对话](how-to-use-conversation-transcription-service.md)。

对于此处显示的代码，您需要**远程对话版本 1.8.0**，仅支持 Windows、Linux 和 Android 上的 Java（1.8.0 或以上）（仅限 API 级别 26 或以上）。

### <a name="obtaining-the-client-sdk"></a>获取客户端 SDK

您可以通过编辑 pom.xml 文件来获取**远程对话**，如下所示。

1. 在文件末尾，在结束标记`</project>`之前，创建一个`repositories`元素，其中引用语音 SDK 的 Maven 存储库：

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. 还添加一`dependencies`个元素，远程对话客户端-sdk 1.8.0作为依赖项：

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

### <a name="sample-transcription-code"></a>示例转录代码

之后，`conversationId`在客户端应用程序中创建远程对话转录客户端**RemoteConversation 转录客户端**，以查询异步转录的状态。 在**远程对话脚本客户端**中使用**get 转录操作**方法获取[PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java)对象。 PollerFlux 对象将包含有关远程操作状态**远程对话转录操作**和最终结果**远程对话转录结果的信息**。 操作完成后，通过在[SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)上调用**getFinalResult**获取**远程对话记录结果**。 在此代码中，我们只需将结果内容打印到系统输出。

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
