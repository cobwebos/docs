---
title: Azure 事件中心-客户端 Sdk |Microsoft Docs
description: 本文提供了有关 Azure 事件中心的客户端 Sdk 的信息。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4c3557556500f2a536e20331bd3d05d84f608f0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312552"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure 事件中心-客户端 Sdk
本文提供了有关 Azure 事件中心支持的 Sdk 的下列信息： 

- 可在应用程序中使用的包的位置 
- GitHub 位置，你可以在其中找到源代码、示例、自述文件、更改日志、报告的问题，还可以提出新问题 
- 入门教程的链接 

## <a name="client-sdks"></a>客户端 SDK
下表描述了当前可用的所有 Azure 事件中心运行时客户端。 虽然其中某些库也包含有限的管理功能，但也有专用于管理操作的特定库。 这些库的核心重点是从事件中心**发送和接收消息**。

| 语言 | 程序包 | 参考 | 
| -------- | ------- | --------------- | 
| . NET Standard （**最新**并支持 .net Core 和 .NET Framework） | [EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[教程](get-started-dotnet-standard-send-v2.md)</li></ul> |
|       | [EventHubs. 处理器](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[教程](get-started-dotnet-standard-send-v2.md)</li></ul> |
| . NET Standard （**旧版**，同时支持 .net Core 和 .NET Framework） | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[教程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework （**旧**） | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[教程](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure 消息传送-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[教程](get-started-java-send-v2.md)</li></ul> |
|      | [eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **（传统）** | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[教程](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[教程](get-started-python-send-v2.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[教程](get-started-python-send-v2.md)</li></ul> |
| JavaScript | [azure/事件中心](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[教程](get-started-node-send-v2.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[教程](get-started-node-send-v2.md)</li></ul> |
| Go | [azure 事件中心-中转](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-go)</li><li>[教程](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-事件中心-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-c)</li><li>[教程](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>管理 SDK
下表列出了所有当前可用的特定于管理的库。 这些库都不包含运行时操作，并且仅用于**管理事件中心实体**。

| 语言 | 程序包 | 参考 | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[教程](get-started-dotnet-standard-send-v2.md)</li></ul> |


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题解答](event-hubs-faq.md)
