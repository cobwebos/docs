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
ms.openlocfilehash: ac928d9087ba5db312540b8ec542d7a2a29e2a99
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179075"
---
| 资源 | 默认限制 |
| --- | :--- |
| 每个订阅的最大群集 | 100 |
| 虚拟机可用性集和基本负载均衡器 SKU 每个群集的最大节点数  | 100 |
| 虚拟机规模集和[标准负载均衡器 SKU][standard-load-balancer]每个群集的最大节点数 | 1000（每个[节点池][node-pool]100 个节点） |
| 每个节点最多箱数：[基本网络][basic-networking]与 Kubenet | 110 |
| 每个节点的最大 pod 数：通过 Azure 容器网络接口的[高级网络][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure 资源管理器模板： 30<sup>1</sup><br />门户部署：30 |

<sup>1</sup>使用 Azure CLI 或资源管理器模板部署 Azure Kubernetes 服务（AKS）群集时，此值可配置为每个节点250个箱。 在已部署 AKS 群集后，或者如果使用 Azure 门户部署群集，则无法在每个节点上配置最大 pod。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
