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
ms.openlocfilehash: 0695416c65eed2bbf0a19d5ed1ea0c53a7ece332
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485426"
---
| Resource | 默认限制 |
| --- | :--- |
| 每个订阅的最大群集数 | 100 |
| 虚拟机可用性集和基本负载均衡器 SKU 每个群集的最大节点数  | 100 |
| 虚拟机规模集和[标准负载均衡器 SKU][standard-load-balancer]每个群集的最大节点数 | 800（每个[节点池][node-pool]100 个节点） |
| 每个节点最多箱数：[基本网络][basic-networking]与 Kubenet | 110 |
| 每个节点的最大 pod 数：通过 Azure 容器网络接口的[高级网络][advanced-networking] | Azure CLI 部署：30<sup>1</sup><br />Azure 资源管理器模板： 30<sup>1</sup><br />门户部署：30 |

<sup>1</sup>使用 Azure CLI 或资源管理器模板部署 Azure Kubernetes 服务 (AKS) 群集时，此值是可以配置的，最大可以配置为每节点 250 个 Pod。 在部署 AKS 群集以后，或者在使用 Azure 门户部署群集的情况下，不能配置每节点的最大 Pod 数。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
