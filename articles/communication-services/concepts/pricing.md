---
title: 呼叫（语音/视频）和聊天的定价方案
titleSuffix: An Azure Communication Services concept document
description: 了解通信服务的定价模型。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 94a34937af92eefa6146c7a043b6d5d68cf6b852
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943388"
---
# <a name="pricing-scenarios"></a>定价方案

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Azure 通信服务的价格基于即用即付模型，无需预付费用。 你只需为服务的消耗和使用付费。

## <a name="voicevideo-calling-and-screen-sharing"></a>语音/视频呼叫和屏幕共享

Azure 通信服务允许向应用程序添加语音/视频呼叫和屏幕共享。 你可以使用 JavaScript、Objective-C (Apple)、Java (Android) 或 .NET 客户端库在应用程序中嵌入该体验。 请参阅我们的[可用客户端库完整列表](./sdk-options.md)。

### <a name="pricing"></a>定价

对于群呼，呼叫和屏幕共享按每个参与者每分钟 0.004 美元进行收费。 若要了解各种可能的呼叫流，请参阅[此页面](./call-flows.md)。

呼叫的每个参与者连接到呼叫的每一分钟都会计入费用。 无论用户是进行视频呼叫、语音呼叫还是屏幕共享，情况都是如此。

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>定价示例：使用 JS 和 iOS 客户端库的音频/视频群呼

Alice 与其同事 Bob 和 Charlie 进行了一次群呼。 Alice 和 Bob 使用 JS 客户端库，Charlie 使用 iOS 客户端库。

- 该呼叫总共持续 60 分钟。
- Alice 和 Bob 参与了整个呼叫。 Alice 将其视频打开了 5 分钟，并将其屏幕共享了 23 分钟。 Bob 在整个呼叫期间（60 分钟）都打开了其视频，并将其屏幕共享了 12 分钟。
- Charlie 在 43 分钟之后离开了呼叫。 Charlie 在其参与的持续时间（43 分钟）内使用了音频和视频。

成本计算

- 2 个参与者 x 60 分钟 x 0.004 美元/参与者/分钟 = 0.48 美元 [视频和音频以相同费率进行收费]
- 1 个参与者 x 43 分钟 x 0.004 美元/参与者/分钟 = 0.172 美元 [视频和音频以相同费率进行收费]

群呼的总成本：0.48 美元 + 0.172 美元 = 0.652 美元

## <a name="chat"></a>聊天

借助通信服务，你可以利用在 2 个或更多用户之间发送和接收聊天消息的功能来增强应用程序。 聊天客户端库适用于 JavaScript、.NET、Python 和 Java。 请参阅[此页面以了解客户端库](./sdk-options.md)

### <a name="price"></a>价格

- 发送每条聊天消息的费用为 0.0008 美元。

### <a name="pricing-example-chat-between-two-users"></a>定价示例：两个用户之间的聊天 

Geeta 启动了与 Emily 的聊天线程以共享更新并发送 5 条消息。 聊天持续了 10 分钟，其中 Geeta 和 Emily 相互发送了 15 条消息。

成本计算 
- 发送的消息数 (5 + 15 + 15) x 0.0008 美元 = 0.028 美元

### <a name="pricing-example-group-chat-with-multiple-users"></a>定价示例：与多个用户进行的群聊 

Charlie 启动了与好友 Casey 和 Jasmine 的聊天线程以规划休假。 他们聊了一段时间，其中 Charlie、Casey 和 Jasmine 分别发送了 20、30 和 18 条消息。 他们意识到其好友 Rose 也可能会对加入旅行感兴趣，因此将她添加到聊天线程中，并与她共享所有消息历史记录。 

Rose 会看到消息并开始聊天。 与此同时，Casey 收到了一个电话，决定稍后再进行对话。 Charlie、Jasmine 和 Rose 决定了旅行日期，并分别发送了另外 30、25、35 条消息。

成本计算 

- 发送的消息数 (20 + 30 + 18 + 30 + 25 + 35) x 0.0008 美元 = 0.1264 美元
