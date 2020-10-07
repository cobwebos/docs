---
title: 快速入门 - 向应用中添加聊天
titleSuffix: An Azure Communication Services quickstart
description: 此快速入门演示如何将通信服务聊天添加到应用。
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665355"
---
# <a name="quickstart-add-chat-to-your-app"></a>快速入门：向应用中添加聊天

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

通过使用通信服务聊天客户端库向应用程序添加实时聊天，开启 Azure 通信服务使用旅程。 在本快速入门中，我们将使用聊天客户端库创建聊天会话，使用户能够彼此进行对话。 若要了解有关聊天概念的详细信息，请访问[聊天概念文档](../../concepts/chat/concepts.md)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你学习了如何：

> [!div class="checklist"]
> * 创建聊天客户端
> * 创建有两个用户参与的会话
> * 向会话发送消息
> * 从会话接收消息
> * 从会话删除用户

> [!div class="nextstepaction"]
> [试用聊天英雄应用](../../samples/chat-hero-sample.md)

你可能还想要：

 - 了解[聊天概念](../../concepts/chat/concepts.md)
 - 熟悉[聊天客户端库](../../concepts/chat/sdk-features.md)
