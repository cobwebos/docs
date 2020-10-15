---
title: 适用于虚拟网络的 Azure PowerShell 示例
description: 了解用于管理虚拟网络的 Azure PowerShell 示例，包括用于为多层应用程序创建虚拟网络的示例。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: b3107d521fb79e3ea6cfe190abd2ec3fb7859cb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288223"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>适用于虚拟网络的 Azure PowerShell 示例

下表包含 Azure PowerShell 脚本的链接：

| Script | 说明 |
|----|----|
| [为多层应用程序创建虚拟网络](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | 创建包含前端和后端子网的虚拟网络。 传入前端子网的流量仅限 HTTP，而传入后端子网的流量限于 SQL、端口 1433。 |
| [两个对等虚拟网络](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | 在同一区域中创建并连接两个虚拟网络。 |
| [通过网络虚拟设备的路由流量](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | 创建包含前端和后端子网的虚拟网络，以及可在这两个子网之间路由流量的 VM。 |
| [筛选入站和出站 VM 网络流量](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | 创建包含前端和后端子网的虚拟网络。 前端子网的入站网络流量仅限于 HTTP 和 HTTPS。 不允许从后端子网到 Internet 的出站流量。 |
|[使用基本负载均衡器配置 IPv4 + IPv6 双堆栈虚拟网络](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|部署具有两个 VM 的双栈 (IPv4+IPv6) 虚拟网络和具有 IPv4 和 IPv6 公共 IP 地址的 Azure 基本负载均衡器。 |
|[使用标准负载均衡器配置 IPv4 + IPv6 双堆栈虚拟网络](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|部署具有两个 VM 的双堆栈 (IPv4+IPv6) 虚拟网络和具有 IPv4 和 IPv6 公共 IP 地址的 Azure 标准负载均衡器。 |
