---
title: include 文件
description: include 文件
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666994"
---
| 资源 | 默认限制 |
| --- | :--- |
| 每个订阅的最大群集数 | 100 |
| 每个群集的最大节点数 | 100 |
| 每个节点的最大 Pod 数：带 Kubenet 的[基本网络][basic-networking] | 110 |
| 每个节点的最大 Pod 数：带 Azure CNI 的[高级网络][advanced-networking] | Azure CLI 部署：110<sup>1</sup><br />资源管理器模板：110<sup>1</sup><br />门户部署：30 |

<sup>1</sup> 使用 Azure CLI 或资源管理器模板部署 AKS 群集时，可以在群集部署中配置此值。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
