---
title: Azure 通信服务聊天客户端库概述
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure 通信服务聊天客户端库。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: fd62a7fed0c6dec818bf4e2558945fb24c9d5261
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943423"
---
# <a name="chat-client-library-overview"></a>聊天客户端库概述

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure 通信服务聊天客户端库可用于向应用程序添加丰富的实时聊天。

## <a name="chat-client-library-capabilities"></a>聊天客户端库功能

下表列出了通信服务聊天客户端库中当前可用的一组功能。

| 功能组 | 功能                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| 核心功能 | 在 2 个或更多用户（最多 250 个用户）之间创建聊天会话                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 更新聊天会话的主题                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | 从聊天会话中添加或删除成员                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 选择是否与新添加的成员共享聊天消息历史记录 - 全部/无/直到特定时间 | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | 获取所有聊天成员会话的列表                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 删除聊天会话                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 获取用户的聊天会话成员身份列表                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 获取特定聊天会话的信息                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 在聊天会话中发送和接收消息                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | 在发送消息后编辑该消息的内容                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 删除消息                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | 发送时将消息的优先级标记为正常或高                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 在成员已阅读消息后发送和接收阅读回执 <br/> 当聊天会话中有超过 20 个成员时不可用    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | 当成员正在聊天会话中主动键入消息时发送和接收键入通知 <br/> 当聊天会话中有超过 20 个成员时不可用      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | 获取聊天会话中的所有消息 <br/> 支持 Unicode 表情符号                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | 将表情符号作为消息内容的一部分发送                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|实时信号（由专用信号包启用）| 当用户在其所属的聊天会话中收到新消息时收到通知                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | 当某个消息由其所属聊天会话中的另一个成员编辑时收到通知                | ✔️   | ❌    | ❌    | ❌  |
|                    | 当某个消息被其所属聊天会话中的另一个成员删除时收到通知                | ✔️   | ❌    | ❌    | ❌  |
|                    | 当另一个聊天会话成员正在键入时收到通知                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | 当另一个成员在聊天会话中阅读消息时得到通知（阅读回执）                               | ✔️   | ❌    | ❌    | ❌  |
| 事件             | 使用事件网格订阅聊天会话中发生的用户活动，并集成自定义通知服务或业务逻辑     | ✔️   | ✔️  | ✔️    | ✔️  |
| 监视        | 监视与发送的消息有关的使用情况                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | 监视应用发出的 API 请求的质量和状态，并通过门户配置警报                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|其他功能 | 使用[认知服务 API](https://docs.microsoft.com/azure/cognitive-services/) 和聊天客户端库启用智能功能 - 对客户端上的传入消息进行语言翻译和情绪分析，在成员讲话时将语音转换为文本以撰写消息等。                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [聊天入门](../../quickstarts/chat/get-started.md)

你可能会对下列文档感兴趣：

- 熟悉[聊天的概念](../chat/concepts.md)
