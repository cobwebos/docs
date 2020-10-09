---
title: Azure 通信服务中的聊天概念
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务聊天概念。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1dd3781b18f82a96f388b0e619ce62b45752a870
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292466"
---
# <a name="chat-concepts"></a>聊天概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure 通信服务聊天客户端库可用于向应用程序添加实时文本聊天。 此页汇总了重要的聊天概念和功能。

若要详细了解特定客户端库语言和功能，请参阅[通信服务聊天客户端库概述](./sdk-features.md) 。

## <a name="chat-overview"></a>聊天概述 

聊天对话在聊天线程中进行。 聊天线程可以包含多个消息和多个用户。 每条消息都属于单个会话，用户可以是一个或多个会话的一部分。 

聊天线程中的每个用户称为成员。 一个聊天线程最多可以具有 250 个成员。 只有会话成员才能发送和接收消息，或在聊天线程中添加/删除成员。 允许的最大消息大小大约为 28 KB。 可以使用 `List/Get Messages` 操作检索聊天线程中的所有消息。 通信服务会存储聊天历史记录，直到你对聊天线程或消息执行删除操作，或者直到聊天线程中没有剩余成员为止，此时聊天线程会被孤立并进行删除处理。   

对于具有 20 个以上成员的聊天线程，已禁用已读回执和键入指示器功能。 

## <a name="chat-architecture"></a>聊天体系结构

聊天体系结构有两个核心部分：1) 可信服务和 2) 客户端应用程序。

:::image type="content" source="../../media/chat-architecture.png" alt-text="显示通信服务聊天体系结构的关系图。":::

 - 可信服务：若要正确管理聊天会话，你需要一种服务，可帮助你使用资源连接字符串连接到通信服务。 此服务负责创建聊天线程、管理线程成员身份并向用户提供访问令牌。 有关访问令牌的详细信息可以在[访问令牌](../../quickstarts/access-tokens.md)快速入门中找到。

 - 客户端应用：客户端应用程序连接到可信服务，并接收用于直接连接到通信服务的访问令牌。 建立此连接之后，客户端应用可以发送和接收消息。
    
## <a name="message-types"></a>消息类型

通信服务聊天共享用户生成的消息以及系统生成的消息，称为线程活动。 在更新聊天线程时会生成线程活动。 对聊天线程调用 `List Messages` 或 `Get Messages` 时，结果会包含用户生成的短信以及系统消息（按时间顺序）。 这有助于你确定添加或删除成员的时间或是聊天线程主题的更新时间。 支持的消息类型包括：  

 - `Text`：用户在聊天对话过程中撰写和发送的实际消息。 
 - `ThreadActivity/AddMember`：指示一个或多个成员已添加到聊天线程的系统消息。 例如：

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`：指示成员已从聊天线程中删除的系统消息。 例如：

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`：指示主题已更新的系统消息。 例如：

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>实时信号 

聊天 JavaScript 客户端库包含实时信号。 这使客户端可以侦听实时更新以及到聊天线程的传入消息，而不必轮询 API。 可用事件包括：

 - `ChatMessageReceived` - 当新消息发送到用户所属的聊天线程时。 对于在上一个主题中讨论的自动生成的系统消息，不会发送此事件。  
 - `ChatMessageEdited` - 当在用户所属的聊天线程中编辑消息时。 
 - `ChatMessageDeleted` - 当在用户所属的聊天线程中删除消息时。 
 - `TypingIndicatorReceived` - 当其他成员在用户所属的聊天线程中键入消息时。 
 - `ReadReceiptReceived` - 当其他成员阅读了用户在聊天线程中发送的消息时。 

## <a name="chat-events"></a>聊天事件 

实时信号使用户可以实时聊天。 服务可以使用 Azure 事件网格订阅与聊天相关的事件。 有关更多详细信息，请参阅[事件处理概念](../event-handling.md)。

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>将认知服务与聊天客户端库配合使用以启用智能功能

可以将 [Azure 认知 API](https://docs.microsoft.com/azure/cognitive-services/) 与聊天客户端库配合使用，将智能功能添加到应用程序。 例如，你能够：

- 使用户可以通过不同的语言互相聊天。 
- 通过检测来自客户的传入问题的负面情绪，帮助支持代理设置票证优先级。
- 分析传入消息以进行密钥检测和实体识别，并基于消息内容在应用中向用户提示相关信息。

实现此目标的一种方法是让可信服务充当聊天线程的成员。 假设要启用语言翻译。 此服务负责侦听其他成员 [1] 所交换的消息，调用认知 API 将内容翻译为所需语言 [2,3]，并在聊天线程中将翻译后的结果作为消息进行发送 [4]。 

这样，消息历史记录将同时包含原始消息和已翻译消息。 在客户端应用程序中，可以添加逻辑以显示原始消息或已翻译消息。 请参阅[本快速入门](https://docs.microsoft.com/azure/cognitive-services/translator/quickstart-translate)以了解如何使用认知 API 将文本翻译为不同的语言。 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="显示通信服务聊天体系结构的关系图。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [聊天入门](../../quickstarts/chat/get-started.md)

你可能会对下列文档感兴趣：

- 熟悉[聊天客户端库](sdk-features.md)
