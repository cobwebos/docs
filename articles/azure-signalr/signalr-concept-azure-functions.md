---
title: 利用 Azure Functions 和 Azure SignalR 服务构建实时应用程序
description: 在无服务器应用程序中使用 Azure SignalR 服务的概述。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 6a71647d452597c84e0df1beb6e9c6c3932d171b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639926"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>利用 Azure Functions 和 Azure SignalR 服务构建实时应用程序

Azure SignalR Service 和 Azure Functions 都是高度可缩放的完全托管服务，使你可以专注于构建应用程序而不是管理基础结构，因此通常将这两种服务结合使用以在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中提供实时通信。

> [!NOTE]
> 了解如何在交互式教程中同时使用 SignalR 和 Azure Functions,[使用 Azure Functions 和 SignalR 服务在 web 应用程序中启用自动更新](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)。

## <a name="integrate-real-time-communications-with-azure-services"></a>将实时通信与 Azure 服务集成在一起

Azure Functions 允许你以[多种语言](../azure-functions/supported-languages.md)(包括 JavaScript、Python、 C#和 Java) 编写代码, 该代码会在云中发生事件时触发。 这些事件的示例包括：

* HTTP 和 Webhook 请求
* 周期性定时器
* Azure 服务中的事件，例如：
    - 事件网格
    - 事件中心
    - 服务总线
    - Cosmos DB 更改源
    - 存储 - blob 和队列
    - 逻辑应用连接器，例如 Salesforce 和 SQL Server

通过使用 Azure Functions 将这些事件与 Azure SignalR Service相集成，可在事件发生时通知数以千计的客户端。

可以使用 Azure Functions 和 SignalR Service 实现的实时无服务器消息传递的一些常见情况包括：

* 在实时仪表板或地图上可视化 IoT 设备遥测
* 在 Cosmos DB 中更新文档时更新应用程序中的数据
* 在 Salesforce 中创建新订单时发送应用内通知

## <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions 的 SignalR Service 绑定

通过 Azure Functions 的 SignalR Service 绑定，可以使用 Azure Function 应用向连接到 SignalR Service 的客户端发布消息。 客户端可以使用 .NET、JavaScript 和 Java 中可用的 SignalR 客户端 SDK 连接到服务，即将推出更多语言。

### <a name="an-example-scenario"></a>示例方案

使用 Azure Functions 将 Azure Cosmos DB 和 SignalR Service 集成在一起以便在 Cosmos DB 更改源上出现新事件时发送实时消息，这就是使用 SignalR Service 绑定的一个示例。

![Cosmos DB, Azure Functions, SignalR Service](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. 在 Cosmos DB 集合中进行了更改
2. 更改事件传播到 Cosmos DB 更改源
3. 通过使用 Cosmos DB 触发器的更改事件触发 Azure Functions
4. SignalR Service 输出绑定将消息发布到 SignalR Service
5. SignalR Service 将消息发布到所有连接的客户端

### <a name="authentication-and-users"></a>身份验证和用户

使用 SignalR Service 可以向所有客户端或仅向客户端子集（例如属于单个用户的客户端）广播消息。 Azure Functions 的 SignalR Service 绑定可以与应用服务身份验证结合，以使用 Azure Active Directory、Facebook 和 Twitter 等提供程序对用户进行身份验证。 然后，你可以直接向这些经过身份验证的用户发送消息。

## <a name="next-steps"></a>后续步骤

在本文中，你将大致了解如何结合使用 Azure Functions 和 SignalR Service 实现各种无服务器实时消息传递的情景。

有关如何使用 Azure Functions 和 SignalR 服务的完整详细信息, 请访问以下资源:

* [SignalR 服务的 Azure Functions 开发和配置](signalr-concept-serverless-development-config.md)
* [使用 Azure Functions 和 SignalR 服务在 web 应用程序中启用自动更新](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

请按照以下其中一个快速入门了解详细信息。

* [Azure SignalR Service 无服务器快速入门 - C#](signalr-quickstart-azure-functions-csharp.md)
* [Azure SignalR Service 无服务器快速入门 - JavaScript](signalr-quickstart-azure-functions-javascript.md)
