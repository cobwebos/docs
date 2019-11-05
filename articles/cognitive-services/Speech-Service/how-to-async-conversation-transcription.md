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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506974"
---
# <a name="asynchronous-conversation-transcription-preview"></a>异步对话脚本（预览）

在本文中，使用**RemoteConversationTranscriptionClient** API 演示了异步对话脚本。 如果已将会话脚本配置为执行异步脚本并具有 `conversationId`，则可以使用**RemoteConversationTranscriptionClient** API 获取与 `conversationId` 关联的脚本。

## <a name="asynchronous-vs-real-time--asynchronous"></a>异步与实时 + 异步

对于异步脚本，会流式传输会话音频，但不需要实时返回脚本。 相反，发送音频后，使用 `ConversationTranscriber` 的 `conversationId` 来查询异步脚本的状态。 异步脚本准备就绪后，你将获得 `RemoteConversationTranscriptionResult`。

通过实时和异步，你可以实时地获取脚本，但也可以通过使用 `conversationId` 进行查询来获得脚本（类似于异步方案）。

完成异步脚本需要执行两个步骤。 第一步是上传音频，只选择 "异步" 或 "实时" 和 "异步"。 第二步是获取脚本结果。

## <a name="upload-the-audio"></a>上传音频

在可以执行异步脚本之前，需要使用 Microsoft 认知语音客户端 SDK （版本1.8.0 或更高版本）将音频发送到对话脚本。

此示例代码演示如何为仅限异步模式创建会话 transcriber。 若要将音频流式传输到 transcriber，需要[通过语音 SDK 实时添加从转录对话](how-to-use-conversation-transcription-service.md)派生的音频流代码。 请参阅该主题的 "**限制**" 部分，查看支持的平台和语言 api。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

对于此处所示的代码，你需要**remoteconversation 版本 1.0.0**，仅在 Windows、Linux 和 Android 上的 Java （1.8 或更高版本）支持（API 级别26或更高版本）。

### <a name="obtaining-the-client-sdk"></a>获取客户端 SDK

可以通过按如下所示编辑 pom .xml 文件来获取**remoteconversation** 。

- 在文件末尾，在结束标记 `</project>`之前，请创建一个 `repositories` 元素，其中包含对语音 SDK 的 Maven 存储库的引用：

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- 另外添加一个 `dependencies` 元素，将 remoteconversation 1.0.0 作为依赖项：

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- 保存更改

### <a name="sample-transcription-code"></a>示例脚本代码

`conversationId`后，在客户端创建一个远程操作对象**RemoteConversationTranscriptionOperation**以查询异步脚本的状态。 **RemoteConversationTranscriptionOperation**是从[轮询](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java)器扩展而来的。 完成轮询器后，请通过订阅轮询器并查询对象来获取**RemoteConversationTranscriptionResult** 。 在此代码中，我们只是将结果内容打印到系统输出。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
