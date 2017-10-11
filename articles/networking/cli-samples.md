---
title: "Azure CLI 示例 |Microsoft 文档"
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
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cli-samples-for-networking"></a>用于联网的 azure CLI 示例

下表包含指向 bash 脚本使用 Azure CLI 生成的链接。

| | |
|-|-|
|**Azure 资源之间的连接**||
| [创建虚拟网络以建立多层应用程序](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 使用前端和后端的子网中创建虚拟网络。 限制为 MySQL，端口 3306 到后端子网的流量时，到前端子网的流量仅限于 HTTP 和 SSH。 |
| [对等两个虚拟网络](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建并连接同一区域中的两个虚拟网络。 |
| [通过网络虚拟设备路由流量](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建与前端和后端的子网和 VM 可以两个子网之间路由流量的虚拟网络。 |
| [筛选入站和出站的 VM 网络流量](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 使用前端和后端的子网中创建虚拟网络。 前端子网的入站的网络流量仅限于 HTTP、 HTTPS 和 SSH。 不允许与后端子网到 Internet 的出站通信。 |
|**负载平衡和流量的方向**||
| [流量进行负载平衡到 Vm 以实现高可用性](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建多个中高度可用的虚拟机和负载平衡配置。 |
| [在 Vm 上的多个网站进行负载平衡](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | 创建具有多个 IP 配置，联接到一个 Azure 可用性集，通过 Azure 负载平衡器可访问的两个 Vm。 |
| [将流量定向跨越多个区域的应用程序的高可用性](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  创建两个 app service 计划、 两个 web 应用、 流量管理器配置文件时，和两个流量管理器终结点。 |
| | |
