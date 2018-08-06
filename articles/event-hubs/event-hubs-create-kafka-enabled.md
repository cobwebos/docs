---
title: 创建启用了 Apache Kafka 的 Azure 事件中心 | Microsoft Docs
description: 使用 Azure 门户创建启用了 Kafka 的 Azure 事件中心命名空间
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 79b6b879bd2332c044ce871e2c9a938c6b9c900c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285067"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>创建启用了 Apache Kafka 的事件中心

Azure 事件中心是大数据流平台即服务 (PaaS)，它每秒引入数百万个事件，可为实时分析和可视化效果提供低延迟和高吞吐量的性能。

Azure 事件中心提供了一个 Kafka 终结点。 此终结点允许事件中心命名空间以本机方式了解 [Apache Kafka](https://kafka.apache.org/intro) 消息协议和 API。 使用此功能，可以像与 Kafka 主题通信那样与事件中心通信，而无需更改协议客户端或运行自己的群集。 事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html) 及更高版本。

本文介绍如何创建事件中心命名空间，以及如何获取将 Kafka 应用程序连接到启用 Kafka 的事件中心所需的连接字符串。

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>创建启用了 Kafka 的事件中心命名空间

1. 登录到 [Azure 门户][Azure portal]，单击屏幕左上角的“创建资源”。

2. 搜索事件中心并选择此处显示的选项：
    
    ![在门户中搜索事件中心](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. 提供唯一名称并在命名空间上启用 Kafka。 单击“创建”。
    
    ![创建命名空间](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. 创建命名空间后，在“设置”选项卡上，单击“共享访问策略”来获取连接字符串。

    ![单击“共享访问策略”](./media/event-hubs-create/create-event-hub7.png)

5. 你可以选择默认的 RootManageSharedAccessKey，或添加新策略。 单击策略名称，复制连接字符串。 
    
    ![选择策略](./media/event-hubs-create/create-event-hub8.png)
 
6. 将此连接字符串添加到 Kafka 应用程序配置中。

现在，可以从使用 Kafka 协议的应用程序，将事件流式传输到事件中心。

## <a name="next-steps"></a>后续步骤

若要了解有关事件中心的详细信息，请访问以下链接：

* [从 Kafka 应用程序流式传输到事件中心](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [了解适用于 Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
* [了解事件中心](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
