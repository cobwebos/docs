---
title: "开始在 .NET 中使用 Azure 中继混合连接 | Microsoft 文档"
description: "为 Azure 中继混合连接编写 C# 控制台应用程序。"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 1af23bfd46dd7d3781505473f7c1d86e65ea9bc7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/10/2017


---

# 中继混合连接入门
<a id="get-started-with-relay-hybrid-connections" class="xliff"></a>
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

本教程简单介绍了 [Azure 中继混合连接](relay-what-is-it.md#hybrid-connections)，同时介绍了如何使用 .NET 创建客户端应用程序，以便将消息发送到相应的侦听器应用程序。 

## 将要完成的任务
<a id="what-will-be-accomplished" class="xliff"></a>
由于混合连接要求客户端和服务器组件，本教程将创建两个控制台应用程序。 下面是相关步骤：

1. 使用 Azure 门户创建中继命名空间。
2. 使用 Azure 门户在该命名空间中创建混合连接。
3. 编写服务器（侦听器）控制台应用程序，用于接收消息。
4. 编写客户端（发送方）控制台应用程序，用于发送消息。

## 先决条件
<a id="prerequisites" class="xliff"></a>

若要完成本教程，需要具备以下先决条件：

1. [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
2. Azure 订阅。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1.使用 Azure 门户创建命名空间
<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>
如果已创建中继命名空间，请跳转到[使用 Azure 门户创建混合连接](#2-create-a-hybrid-connection-using-the-azure-portal)部分。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## 2.使用 Azure 门户创建混合连接
<a id="2-create-a-hybrid-connection-using-the-azure-portal" class="xliff"></a>
如果已创建混合连接，请跳转到[创建服务器应用程序](#3-create-a-server-application-listener)部分。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## 3.创建服务器应用程序（侦听程序）
<a id="3-create-a-server-application-listener" class="xliff"></a>
为侦听和接收来自中继的消息，我们使用 Visual Studio 编写 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## 4.创建客户端应用程序（发送程序）
<a id="4-create-a-client-application-sender" class="xliff"></a>
为将消息发送到中继，我们使用 Visual Studio 编写 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## 5.运行应用程序
<a id="5-run-the-applications" class="xliff"></a>
1. 运行服务器应用程序。
2. 运行客户端应用程序并输入一些文本。
3. 确保服务器应用程序控制台输出了客户端应用程序中输入的文本。

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

祝贺你，现已创建端到端混合连接应用程序。

## 后续步骤：
<a id="next-steps" class="xliff"></a>
* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)


