---
title: "Azure PowerShell 示例 | Microsoft Docs"
description: "Azure PowerShell 示例"
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017

---
# <a name="azure-powershell-samples-for-networking"></a>适用于网络的 Azure PowerShell 示例

下表包含指向使用 Azure PowerShell 生成的脚本的链接。

| | |
|-|-|
|Azure 资源之间的连接||
| [为多层应用程序创建虚拟网络](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络。 传入前端子网的流量仅限 HTTP，而传入后端子网的流量限于 SQL、端口 1433。 |
| [两个对等虚拟网络](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 在同一区域中创建并连接两个虚拟网络。 |
| [通过网络虚拟设备的路由流量](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络，以及可在这两个子网之间路由流量的 VM。 |
| [筛选入站和出站 VM 网络流量](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络。 前端子网的入站网络流量仅限于 HTTP、HTTPS。 从后端子网到 Internet 的出站流量不受限制。 |
|负载均衡和流量方向||
| [为实现高可用性将流量负载均衡至 VM](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [在 VM 上对多个网站进行负载均衡](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建两个具有多个 IP 配置、联接到 Azure 可用性集、可通过 Azure 负载均衡器访问的 VM。 |
| [跨多个区域定向流量，以实现应用程序的高可用性](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  创建两个应用服务计划、两个 Web 应用、一个流量管理器配置文件和两个流量管理器终结点。 |
| | |

