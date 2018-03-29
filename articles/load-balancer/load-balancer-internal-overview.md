---
title: Azure 内部负载均衡器概述 | Microsoft Docs
description: 内部负载均衡器在 Azure 中的工作原理以及配置内部终结点的方案。
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Azure 内部负载均衡器概述



Azure 内部负载均衡器 (ILB) 仅将流量定向到云服务内的资源，或使用 VPN 来访问 Azure 基础结构。 在这一点上，ILB 与面向 Internet 的负载均衡器不同。 Azure 基础结构限制了对云服务或虚拟网络的负载均衡虚拟 IP (VIP) 地址的访问。 VIP 地址和虚拟网络不会直接公开给 Internet 终结点。 内部业务线应用程序可在 Azure 中运行，并可从 Azure 内或从本地资源访问这些应用程序。

## <a name="why-you-might-need-an-internal-load-balancer"></a>需要内部负载均衡器的原因

内部负载均衡器可在驻留于云服务或虚拟网络（具有区域性作用域）中的虚拟机 (VM) 之间进行负载均衡。 若要了解具有区域性作用域的虚拟网络，请参阅 Azure 博客中的[区域虚拟网络](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。 为关联组配置的现有虚拟网络无法使用 ILB。

ILB 允许进行以下类型的负载均衡：

* 在云服务中：从 VM 负载均衡到驻留在同一云服务中的一组 VM。 请参阅此<a href="#figure1">示例</a>。
* 在虚拟网络中：从虚拟网络中的 VM 负载均衡到驻留在该虚拟网络的同一云服务中的一组 VM。 请参阅此<a href="#figure2">示例</a>。
* 对于跨界虚拟网络：从本地计算机负载均衡到驻留在该虚拟网络的同一云服务中的一组 VM。 请参阅此<a href="#figure3">示例</a>。
* 对于多层应用程序：对面向 Internet 的多层应用程序进行负载均衡，其中的后端层不面向 Internet。 后端层需要针对面向 Internet 的层发出的流量进行负载均衡。
* 对于业务线应用程序：使托管在 Azure 中的业务线应用程序实现负载均衡，而无需其他负载均衡器硬件或软件。 此方案将本地服务器包含在一组流量已实现负载均衡的计算机中。

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>面向 Internet 的多层应用程序的负载均衡

Web 层包含 Internet 客户端的面向 Internet 的终结点，是负载均衡集的一部分。 ILB 将来自 TCP 端口 443 (HTTPS) 的 Web 客户端的传入流量分发到 Web 服务器。

数据库服务器位于 ILB 终结点之后，Web 服务器使用该终结点进行存储。 ILB 终结点是数据库服务负载均衡的终结点。 流量跨 ILB 集中的数据库服务器进行负载均衡。

下图显示同一云服务内面向 Internet 的多层应用程序的内部负载均衡。

<a name="figure1"></a>
![面向 Internet 的多层应用程序](./media/load-balancer-internal-overview/IC736321.png)

还有另一方案适用于多层应用程序。 即，将负载均衡器部署到使用 ILB 服务的云服务以外的其他云服务。

使用同一虚拟网络的云服务可访问该 ILB 终结点。 下图显示与数据库后端处于不同云服务中的前端 Web 服务器。 前端服务器使用与后端相同的虚拟网络中的 ILB 终结点。

<a name="figure2"></a>
![不同云服务中的前端服务器](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Intranet 业务线应用程序的负载均衡

本地网络中客户端发出的流量跨业务线服务器集进行负载均衡，该服务器集使用到 Azure 网络的 VPN 连接。

客户端计算机可使用点到站点 VPN 通过 Azure VPN 服务访问 IP 地址。 可在 ILB 终结点后面托管业务线应用程序。

<a name="figure3"></a>
![ILB 终结点后面托管的业务线应用程序](./media/load-balancer-internal-overview/IC744148.png)

业务线应用程序的另一方案是通过站点到站点 VPN 连接到在其中配置了 ILB 终结点的虚拟网络。 将本地网络流量路由到 ILB 终结点。

<a name="figure4"></a>
![将本地网络流量路由到 ILB 终结点](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>限制

内部负载均衡器配置不支持 SNAT。 在本文中，SNAT 是指涉及端口伪装源网络地址转换的方案。 负载均衡器池中的 VM 必须访问各自内部负载均衡器的前端 IP 地址。 当流负载均衡到发出流的 VM 时，连接会失败。 ILB 不支持这些方案。 必须改为使用代理样式的负载均衡器。

## <a name="next-steps"></a>后续步骤

* [Azure 负载均衡器的 Azure 资源管理器支持](load-balancer-arm.md)
* [开始配置面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)
* [配置内部负载均衡器入门](load-balancer-get-started-ilb-arm-ps.md)
* [配置负载均衡器分发模式](load-balancer-distribution-mode.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
