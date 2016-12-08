---
title: "什么是 Azure 中继？ | Microsoft 文档"
description: "Azure 中继概述"
services: service-bus-relay
documentationcenter: .net
author: banisadr
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: babanisa,sethm
translationtype: Human Translation
ms.sourcegitcommit: 8ba8baa1b6500b3300e0bfc1c6b9f00f214f0179
ms.openlocfilehash: 4329e0f99828f5a0651e6b03be0813d9e68debaa


---
# <a name="what-is-azure-relay"></a>什么是 Azure 中继？
Azure 中继服务简化了混合应用程序，通过允许安全地向公有云公开位于企业网络内的服务，无需打开防火墙连接，也无需对企业网络基础结构进行彻底更改。 中继支持各种不同的传输协议和 Web 服务标准。

中继服务支持传统的单向流量、请求/响应流量和对等流量。 它还支持 Internet 范围的事件分发，以实现发布/订阅方案和双向套接字通信，从而提高点到点通信效率。 

在中继数据传输模式中，本地服务会通过出站端口连接至中继服务，并创建双向套接字用于绑定至特定集合地址的通信。 客户端然后可以通过将流量发送到抵达会合地址的中继服务来与本地服务通信。 中继服务接着通过每个客户端专用的双向套接字将数据“中继”到本地服务。 客户端不需要与本地服务建立直接连接，也不需要了解服务所在的位置，并且本地服务无需在防火墙上打开任何入站端口。

中继提供的关键功能要素是使用类似于 TCP 的限制、终结点发现、连接状态和叠加的终结点安全性，跨网络边界实现双向非缓冲通信。 中继功能与 VPN 等网络级集成技术的不同之处在于，中继可以划归到单个计算机上的单个应用程序终结点，而 VPN 技术的侵入性则要大得多，因为它依赖于改变网络环境。

Azure 中继具有两项功能：

1. [混合连接](#hybrid-connections) - 使用开放标准 Web 套接字，实现多平台方案。
2. [WCF 中继](#wcf-relays) - 使用 Windows Communication Foundation (WCF) 实现远程过程调用。 WCF 中继是传统的中继产品，很多客户可能已在其 WCF 编程模型中使用。

使用混合连接和 WCF 中继都能与企业网络中的资产建立安全连接。 哪一种功能更好将取决于具体的需求，如下表中所述：

|  | WCF 中继 | 混合连接 |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |
| **JavaScript/NodeJS*** | |x |
| **Java*** | |x |
| **基于标准的开放协议** | |x |
| **多个 RPC 编程模型** | |x |

*正式版

## <a name="hybrid-connections"></a>混合连接
[Azure 中继混合连接](relay-hybrid-connections-protocol.md)功能是在现有中继功能的基础上演进的安全开放协议，可在包含基本 WebSocket 功能（明确包括常用 Web 浏览器中的 WebSocket API）的任何平台和任何语言中实现。 混合连接基于 HTTP 和 WebSocket。

## <a name="wcf-relays"></a>WCF 中继
WCF 中继适用于整个 .NET Framework (NETFX) 和 WCF。 可以使用一套 WCF“中继”绑定在本地服务与中继服务之间发起连接。 在幕后，中继绑定将映射到新的传输绑定元素，这些元素旨在创建与云中服务总线集成的 WCF 通道组件。

## <a name="service-history"></a>服务历史记录
混合连接取代了以前的“BizTalk 服务”功能，后者构建在 Azure 服务总线 WCF 中继的基础之上。 新的混合连接功能为现有 WCF 中继做了补充，在可预见的未来，这两项服务功能将在中继服务中共存；它们共享一个公用网关，但实现方式有所不同。

## <a name="next-steps"></a>后续步骤：
* [中继常见问题](relay-faq.md)
* [创建命名空间](relay-create-namespace-portal.md)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->


