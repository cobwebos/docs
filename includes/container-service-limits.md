---
title: include 文件
description: include 文件
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335056"
---
| 资源 | 限制 |
| --- | :--- |
| 每个订阅的最大群集数 | 100 |
| 具有虚拟机可用性集和基本负载均衡器 SKU 的每个群集的最大节点  | 100 |
| 具有虚拟机缩放集和[标准负载均衡器 SKU][standard-load-balancer]的每个群集的最大节点 | 1000 （每个节点池 100[个节点][node-pool]） |
| 每个节点的最大窗格：与 Kubenet[的基本网络][basic-networking] | 110 |
| 每个节点的最大窗格：使用 Azure 容器[网络接口的高级网络][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure 资源管理器模板： 30<sup>1</sup><br />门户部署：30 |

<sup>1</sup>使用 Azure CLI 或资源管理器模板部署 Azure Kubernetes 服务 (AKS) 群集时，此值是可以配置的，最大可以配置为每节点 250 个 Pod。 在部署 AKS 群集以后，或者在使用 Azure 门户部署群集的情况下，不能配置每节点的最大 Pod 数。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
