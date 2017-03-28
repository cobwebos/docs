---
title: "开始在 .NET 中使用 Azure 中继混合连接 | Microsoft 文档"
description: "如何为混合连接编写 C# 控制台应用程序"
services: service-bus-relay
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/27/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: d27016559ede5d810d7efcec2a3abc78334f0f0a
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-relay-hybrid-connections"></a>中继混合连接入门
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

本教程简单介绍了 [Azure 中继混合连接](relay-what-is-it.md#hybrid-connections)，同时介绍了如何创建客户端应用程序，以便将消息发送到相应的侦听器应用程序。 

## <a name="what-will-be-accomplished"></a>将要完成的任务
由于混合连接要求客户端和服务器组件，本教程将创建两个控制台应用程序。 步骤如下：

1. 使用 Azure 门户创建中继命名空间。
2. 使用 Azure 门户创建混合连接。
3. 编写服务器（侦听器）控制台应用程序，用于接收消息。
4. 编写客户端（发送方）控制台应用程序，用于发送消息。

## <a name="prerequisites"></a>先决条件
1. [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2015。
2. Azure 订阅。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 门户创建命名空间
如果已创建中继命名空间，请跳转到[使用 Azure 门户创建混合连接](#2-create-a-hybrid-connection-using-the-azure-portal)部分。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.使用 Azure 门户创建混合连接
如果已创建混合连接，请跳转到[创建服务器应用程序](#3-create-a-server-application-listener)部分。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.创建服务器应用程序（侦听程序）
为侦听和接收来自中继的消息，我们使用 Visual Studio 编写 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.创建客户端应用程序（发送程序）
为将消息发送到中继，我们使用 Visual Studio 编写 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.运行应用程序
1. 运行服务器应用程序。
2. 运行客户端应用程序并输入一些文本。
3. 确保服务器应用程序控制台输出了客户端应用程序中输入的文本。

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

祝贺你，现已创建端到端混合连接应用程序。

## <a name="next-steps"></a>后续步骤：
* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)


