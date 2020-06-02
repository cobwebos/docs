---
title: 已启用 Azure Arc 的 Kubernetes 概述
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes、Arc、Azure、容器
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665271"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>什么是已启用 Azure Arc 的 Kubernetes（预览版）

通过已启用 Azure Arc 的 Kubernetes（预览版），可以在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集附加到 Azure Arc 后，它将显示在 Azure 门户中，并具有 Azure 资源管理器 ID 和托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。 


如果要将 Kubernetes 群集连接到 Azure，需要群集管理员部署代理。 这些代理在名为 `azure-arc` 的 Kubernetes 命名空间中运行，并且是标准 Kubernetes 部署。 代理负责连接到 Azure、收集 Azure Arc 日志和指标并监视配置请求。  
 
 > [!NOTE]
> 已启用 Azure Arc 的 Kubernetes 为预览版，不建议用于生产工作负载。 


## <a name="supported-scenarios"></a>支持的方案 

已启用 Azure Arc 的 Kubernetes 支持以下方案： 

* 连接在 Azure 外部运行的 Kubernetes，以便用于库存、分组和标记 

* 使用基于 GitOps 的配置管理来部署应用程序并应用配置 

* 使用适用于容器的 Azure Monitor 来查看和监视群集 

* 使用适用于 Kubernetes 的 Azure Policy 来应用策略 

 
## <a name="supported-regions"></a>支持的区域 

以下区域目前支持已启用 Azure Arc 的 Kubernetes： 

* 美国东部 
* 西欧 


## <a name="next-steps"></a>后续步骤

* [连接群集](./connect-cluster.md)
