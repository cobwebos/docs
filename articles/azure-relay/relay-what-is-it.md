---
title: 什么是 Azure 中继？ | Microsoft Docs
description: 本文提供 Azure 中继服务的概述。借助该服务，无需打开防火墙连接或者对网络基础设施进行彻底的改造，就能开发使用企业网络中运行的本地服务的云应用程序。
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 964a472a5c0a6350090f83755747a12e89a1650e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211627"
---
# <a name="what-is-azure-relay"></a>什么是 Azure 中继？
使用 Azure 中继服务可以安全地在公有云中公开企业网络中运行的服务。 无需打开防火墙上的端口或者对企业网络基础设施进行彻底的改造就能实现此目的。 

中继服务支持在本地服务与云中或其他本地环境中运行的应用程序之间使用以下方案。 

- 传统的单向请求/响应式通信和对等通信 
- Internet 范围的事件分发，实现发布/订阅方案 
- 跨网络边界的双向无缓冲套接字通信。

Azure 中继不同于 VPN 等网络级集成技术。 Azure 中继的范围可以限定于一台计算机上的单个应用程序终结点。 VPN 技术的侵入性更强，因为它依赖于对网络环境的改造。 

## <a name="basic-flow"></a>基本流程
在中继数据传输模式下，涉及的基本步骤如下：

1. 本地服务通过出站端口连接到中继服务。 
2. 该服务创建双向套接字，以便专门为特定的地址进行通信。 
3. 然后，客户端可以通过将流量发送到针对该地址的中继服务来与本地服务通信。 
4. 中继服务接着通过客户端专用的双向套接字将数据中继到本地服务。  客户端不需要与本地服务建立直接连接。 它不需要知道该服务的位置。 另外，本地服务不需要在防火墙中打开任何入站端口。


## <a name="features"></a>功能 
Azure 中继具有两项功能：

- [混合连接](#hybrid-connections) - 使用开放标准 Web 套接字，实现多平台方案。
- WCF 中继 - 使用 Windows Communication Foundation (WCF) 实现远程过程调用。 WCF 中继是传统的中继产品，很多客户已在其 WCF 编程模型中使用。

## <a name="hybrid-connections"></a>混合连接

Azure 中继的混合连接功能是以往的中继功能的安全开放协议演进。 可在任何平台中以任何语言使用此功能。 Azure 中继的混合连接功能基于 HTTP 和 WebSocket 协议。 此功能允许通过 Web 套接字或 HTTP(S) 发送请求和接收响应。 此功能与常用 Web 浏览器中的 WebSocket API 兼容。 

有关混合连接协议的详细信息，请参阅[混合连接协议指南](relay-hybrid-connections-protocol.md)。 可将混合连接与任何运行时/语言的任何 Web 套接字库配合使用。

> [!NOTE]
> Azure 中继的混合连接取代了 BizTalk 服务的旧式混合连接功能。 BizTalk 服务的混合连接功能构建在 Azure 服务总线 WCF 中继的基础之上。 Azure 中继的混合连接功能对现存的 WCF 中继功能做了补充。 这两项服务功能（WCF 中继和混合连接）在 Azure 中继服务中并存。 它们共享一个公用网关，但实现方式有所不同。

## <a name="wcf-relay"></a>WCF 中继
WCF 中继适用于整个 .NET Framework 和 WCF。 可以使用一套 WCF“中继”绑定在本地服务与中继服务之间建立连接。 中继绑定将映射到新的传输绑定元素，这些元素旨在创建与云中服务总线集成的 WCF 通道组件。 有关详细信息，请参阅 [WCF 中继入门](service-bus-relay-tutorial.md)。

## <a name="hybrid-connections-vs-wcf-relay"></a>混合连接与 WCF 中继
使用混合连接和 WCF 中继都能与企业网络中的资产建立安全连接。 哪一种功能更好将取决于具体的需求，如下表中所述：

|  | WCF 中继 | 混合连接 |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Java script/Node.JS** | |x |
| **基于标准的开放协议** | |x |
| **RPC 编程模型** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>体系结构：处理传入中继请求
下图演示了 Azure 中继服务如何处理传入的中继请求：

![处理传入 WCF 中继请求](./media/relay-what-is-it/ic690645.png)

1. 侦听方客户端将侦听请求发送到 Azure 中继服务。 Azure 负载均衡器将请求路由到某个网关节点。 
2. Azure 中继服务在网关存储中创建中继。 
3. 发送方客户端发送连接侦听方服务的请求。 
4. 收到请求的网关在网关存储中查找中继。 
5. 网关将连接请求转发到网关存储中提到的适当网关。 
6. 网关将请求发送到侦听方客户端，让该客户端与最靠近发送方客户端的网关节点建立临时通道。 
7. 侦听客户端会创建一个临时通道，通往最靠近发送客户端的网关。 通过网关在客户端之间建立连接以后，客户端就可以彼此交换消息了。 
8. 网关将来自侦听客户端的任何消息转发到发送客户端。 
9. 网关将来自发送客户端的任何消息转发到侦听客户端。  

## <a name="next-steps"></a>后续步骤
* [.NET Websocket 入门](relay-hybrid-connections-dotnet-get-started.md)
* [.NET HTTP 请求入门](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [节点 Websocket 入门](relay-hybrid-connections-node-get-started.md)
* [Node HTTP 请求入门](relay-hybrid-connections-http-requests-node-get-started.md)
* [中继常见问题](relay-faq.md)

