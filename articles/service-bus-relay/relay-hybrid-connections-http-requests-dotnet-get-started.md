---
title: 开始在 .NET 中使用 Azure 中继混合连接 HTTP 请求 | Microsoft Docs
description: 使用 .NET 为 Azure 中继混合连接 HTTP 请求编写 C# 控制台应用程序。
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 743e5c5a44f2ed9e6f6d2df9388ef3f01c501bff
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>开始在 .NET 中使用中继混合连接 HTTP 请求
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

本教程简单介绍了 [Azure 中继混合连接](relay-what-is-it.md#hybrid-connections)。 了解如何使用 Microsoft .NET 创建客户端应用程序，以便将请求发送到相应的侦听器应用程序。 

## <a name="what-will-be-accomplished"></a>将要完成的任务
混合连接要求使用客户端组件和服务器组件。 在本教程中，请完成以下步骤，以便创建两个控制台应用程序：

1. 使用 Azure 门户创建中继命名空间。
2. 使用 Azure 门户在该命名空间中创建混合连接。
3. 编写服务器（侦听器）控制台应用程序，用于接收请求。
4. 编写客户端（发送方）控制台应用程序，用于发送请求。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
* Azure 订阅。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1.使用 Azure 门户创建命名空间
如果已创建中继命名空间，请转到[使用 Azure 门户创建混合连接](#2-create-a-hybrid-connection-using-the-azure-portal)。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2.使用 Azure 门户创建混合连接
如果已创建混合连接，请转到[创建服务器应用程序](#3-create-a-server-application-listener)。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.创建服务器应用程序（侦听程序）
在 Visual Studio 中编写可侦听和接收来自中继的消息的 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.创建客户端应用程序（发送程序）
在 Visual Studio 中编写可将消息发送到中继的 C# 控制台应用程序。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.运行应用程序
1. 运行服务器应用程序。
2. 运行客户端应用程序并输入一些文本。
3. 确保服务器应用程序控制台显示了客户端应用程序中输入的文本。

祝贺你，现已创建端到端混合连接应用程序！

## <a name="next-steps"></a>后续步骤

* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)

