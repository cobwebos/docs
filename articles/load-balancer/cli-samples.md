---
title: 适用于负载均衡器的 Azure CLI 示例
titlesuffix: Azure Load Balancer
description: Azure CLI 示例
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: 9567dc1425ea74a9f46912532c5b8e4e4afc2fdb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275688"
---
# <a name="azure-cli-samples-for-load-balancer"></a>适用于负载均衡器的 Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|-|-|
| [为实现高可用性将流量负载均衡至 VM](./scripts/load-balancer-linux-cli-sample-nlb.md) | 使用高度可用且负载均衡的配置创建多个虚拟机。 |
| [跨可用性区域对 VM 进行负载均衡](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | 在区域内的不同可用性区域中创建三个 VM 和具有区域冗余前端 IP 地址的标准负载均衡器。 此负载均衡器配置有助于在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。 |
|[在特定的可用性区域中对 VM 进行负载均衡](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|创建三个 VM 和一个具有区域前端 IP 的标准负载均衡器，该负载均衡器可帮助对齐给定区域的单个区域中的数据路径和资源。|
| [在 VM 上对多个网站进行负载均衡](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | 创建两个具有多个 IP 配置、联接到 Azure 可用性集、可通过 Azure 负载均衡器访问的 VM。 |
| | |

