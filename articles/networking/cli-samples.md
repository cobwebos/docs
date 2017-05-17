---
title: "Azure CLI 示例 | Microsoft 文档"
description: "Azure CLI 示例"
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cli-samples-for-networking"></a>用于网络的 Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
|Azure 资源之间的连接||
| [为多层应用程序创建虚拟网络](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络。 传入前端子网的流量仅限 HTTP 和 SSH，而传入后端子网的流量限于 MySQL、端口 3306。 |
| [两个对等虚拟网络](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 在同一区域中创建并连接两个虚拟网络。 |
| [通过网络虚拟设备的路由流量](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络，以及可在这两个子网之间路由流量的 VM。 |
| [筛选入站和出站 VM 网络流量](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建包含前端和后端子网的虚拟网络。 前端子网的入站网络流量仅限于 HTTP、HTTPS 和 SSH。 从后端子网到 Internet 的出站流量不受限制。 |
|负载均衡和流量方向||
| [为实现高可用性将流量负载均衡至 VM](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [在 VM 上对多个网站进行负载均衡](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建两个具有多个 IP 配置、联接到 Azure 可用性集、可通过 Azure 负载均衡器访问的 VM。 |
| [跨多个区域定向流量，以实现应用程序的高可用性](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  创建两个应用服务计划、两个 Web 应用、一个流量管理器配置文件和两个流量管理器终结点。 |
| | |

