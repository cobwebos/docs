---
title: include 文件
description: include 文件
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62eb75ef18d3ac81be65783e57c21c0aefd7a429
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554802"
---
| 资源 | 默认限制 |
| --- | :--- |
| 每个订阅的最大群集 | 100 |
| 每个群集最大节点数 | 100 |
| 每个节点的最大 pod:带 Kubenet 的[基本网络][basic-networking] | 110 |
| 每个节点的最大 pod:[高级网络][ advanced-networking]与 Azure 容器网络接口 | Azure CLI 部署：30<sup>1</sup><br />Azure 资源管理器模板：30<sup>1</sup><br />门户部署：30 |

<sup>1</sup>部署使用 Azure CLI 或 Resource Manager 模板的 Azure Kubernetes 服务 (AKS) 群集时，此值是可配置多达 110 pod 每个节点。 你已部署 AKS 群集后，或使用 Azure 门户部署群集，不能配置每个节点的最大 pod。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
