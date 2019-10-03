---
title: Azure Service Fabric 网络简介 | Microsoft Docs
description: 了解有关 Service Fabric 网格中的网络、网关和智能流量路由。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811015"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric 网格应用程序中的网络简介
本文介绍了不同类型的负载均衡器、网关如何将网络与你的应用程序连接到其他网络，以及如何在应用程序中的服务之间路由流量。

## <a name="layer-4-vs-layer-7-load-balancers"></a>第 4 层和第 7 层负载均衡器
负载均衡可以在网络 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的不同层上执行，通常在第 4 (L4) 层和第 7 (L7) 层中。  通常情况下，有两种类型的负载均衡器：

- L4 负载均衡器在网络传输层工作，它处理数据包交付，而不考虑数据包的内容。 负载均衡器只检查数据包标头，因此平衡条件仅限于 IP 地址和端口。 例如，客户端发出到负载均衡器的 TCP 连接。 负载均衡器终止连接（通过直接回应 SYN）、选择后端，并发出新的 TCP 连接到后端（发送新 SYN）。 L4 负载均衡器通常仅在 L4 TCP/UDP 连接或会话级别下操作。 因此负载均衡将重定向字节，并确保来自同一会话的字节最终位于相同的后端。 L4 负载均衡器不知道它正在移动的字节的任何应用程序详细信息。 字节可以是任何应用程序协议。

- L7 负载均衡器在应用层工作，它处理每个数据包的内容。 它检查数据包内容，因为它理解 HTTP、HTTPS 或 WebSockets 等协议。 这使得负载均衡器能够执行高级路由。 例如，客户端发出单个 HTTP/2 TCP 连接到负载均衡器。 然后负载均衡器继续进行两个后端连接。 当客户端将两个 HTTP/2 流发送到负载均衡器时，流 1 被发送到后端 1，流 2 则被发送到后端 2。 因此，即使是请求负载差异很大的多路复用客户端，也可以在后端之间有效地进行平衡。 

## <a name="networks-and-gateways"></a>网络和网关
在 [Service Fabric 资源模型](service-fabric-mesh-service-fabric-resources.md)中，网络资源是可单独部署的资源，独立于可能将其称为依赖项的应用程序或服务资源。 它用于为应用程序创建一个对 Internet 开放的网络。 同一网络可以包含来自不同应用程序的多个服务。 此专用网络是由 Service Fabric 创建和管理的，而不是 Azure 虚拟网络 (VNET)。 可以动态地从网络资源中添加和删除应用程序，以启用和禁用 VNET 连接。 

网关用于桥接两个网络。 网关资源部署 [Envoy 代理](https://www.envoyproxy.io/)，它为任何协议提供 L4 路由，为高级 HTTP(S) 应用程序路由提供 L7 路由。 网关将流量从外部网络路由到你的网络，并确定将流量路由到哪个服务。  外部网络可以是开放网络（本质上是公共 Internet），也可以是 Azure 虚拟网络，允许连接其他 Azure 应用程序和资源。 

![网络和网关][Image1]

使用 `ingressConfig` 创建网络资源时，将为网络资源分配一个公共 IP。 公共 IP 将关联到网络资源的生存期。

创建网格应用程序时，它应引用现有的网络资源。 可以添加新的公共端口，也可以从入口配置中删除现有端口。 如果应用程序资源正在引用网络资源，资源删除将失败。 删除应用程序时，将删除网络资源。

## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格，请阅读概述：
- [Service Fabric 网格概述](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png