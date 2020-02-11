---
title: IP 地址 168.63.129.16 是什么？ | Microsoft Docs
description: 了解 IP 地址 168.63.129.16 以及它如何与资源一起工作。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114356"
---
# <a name="what-is-ip-address-1686312916"></a>IP 地址 168.63.129.16 是什么？

IP 地址 168.63.129.16 是虚拟公共 IP 地址，用于简化 Azure 平台资源的通信通道。 客户可以在 Azure 中为其专有虚拟网络定义任何地址空间。 因此，Azure 平台资源必须显示为一个唯一的公共 IP 地址。 此虚拟公共 IP 地址有助于实现以下几个方面：

- 使 VM 代理能够与 Azure 平台通信，以表明它处于“就绪”状态。
- 启用与 DNS 虚拟服务器的通信，以便为没有自定义 DNS 服务器的资源（如 VM）提供筛选的名称解析。 此筛选确保客户只能解析其自己资源的主机名。
- 启用[来自 Azure 负载均衡器的运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)，以确定 vm 的运行状况状态。
- 使 VM 能够从 Azure 中的 DHCP 服务获取动态 IP 地址。
- 为 PaaS 角色启用来宾代理检测信号消息。

## <a name="scope-of-ip-address-1686312916"></a>IP 地址 168.63.129.16 的作用域

公共 IP 地址168.63.129.16 在所有区域和所有国家/地区云中使用。 此特殊的公共 IP 地址由 Microsoft 拥有，不会更改。 建议在任何本地（VM 中）防火墙策略（出站方向）上允许此 IP 地址。 此特殊 IP 地址和资源之间的通信是安全的，因为只有内部 Azure 平台才能从此 IP 地址获得消息。 如果阻止此地址，可能会在各种场景中出现意外行为。 168.63.129.16 是[主机节点的虚拟 IP](../virtual-network/security-overview.md#azure-platform-considerations) ，因此不需要使用用户定义的路由。

- VM 代理需要通过端口80、443、32526 with WireServer （168.63.129.16）进行出站通信。 这些应在 VM 上的本地防火墙中打开。 使用168.63.129.16 的这些端口上的通信不受配置的网络安全组的限制。
- 168.63.129.16 可向 VM 提供 DNS 服务。 如果不需要这种情况，则可以在 VM 上的本地防火墙中阻止此流量。 默认情况下，DNS 通信不受配置的网络安全组的限制，除非专门利用[AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags)服务标记。
- 如果 VM 是负载均衡器后端池的一部分，则应允许[运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)通信来自168.63.129.16。 默认网络安全组配置具有允许此通信的规则。 此规则利用[AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags)服务标记。 如果需要，可以通过配置网络安全组来阻止此流量，但这会导致探测失败。

在非虚拟网络方案（经典）中，运行状况探测源自专用 IP，不使用168.63.129.16。

## <a name="next-steps"></a>后续步骤

- [安全组](security-overview.md)
- [创建、更改或删除网络安全组](manage-network-security-group.md)
