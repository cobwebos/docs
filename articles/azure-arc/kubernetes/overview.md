---
title: 已启用 Azure Arc 的 Kubernetes 概述
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 本文提供已启用 Azure Arc 的 Kubernetes 的概述。
keywords: Kubernetes、Arc、Azure、容器
ms.custom: references_regions
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050059"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>什么是已启用 Azure Arc 的 Kubernetes 预览版？

通过已启用 Azure Arc 的 Kubernetes 预览版，可以在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集附加到 Azure Arc 后，它将显示在 Azure 门户中。 它具有一个 Azure 资源管理器 ID 和一个托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。 

若要将 Kubernetes 群集连接到 Azure，群集管理员需要部署代理。 这些代理在名为 `azure-arc` 的 Kubernetes 命名空间中运行，并且是标准 Kubernetes 部署。 代理负责连接到 Azure、收集 Azure Arc 日志和指标并监视配置请求。 

已启用 Azure Arc 的 Kubernetes 支持行业标准 SSL 来保护传输中的数据。 此外，数据以静态加密方式存储在 Azure Cosmos DB 数据库中，确保数据保密性。
 
> [!NOTE]
> 启用了 Azure Arc 的 Kubernetes 处于预览状态。 建议不要将它用于生产工作负载。

## <a name="supported-scenarios"></a>支持的方案 

已启用 Azure Arc 的 Kubernetes 支持以下方案： 

* 连接在 Azure 外部运行的 Kubernetes，以便用于库存、分组和标记。

* 使用基于 GitOps 的配置管理来部署应用程序并应用配置。 

* 使用适用于容器的 Azure Monitor 来查看和监视群集。 

* 使用适用于 Kubernetes 的 Azure Policy 来应用策略。 

## <a name="supported-regions"></a>支持的区域 

以下区域目前支持已启用 Azure Arc 的 Kubernetes： 

* 美国东部 
* 西欧 

## <a name="next-steps"></a>后续步骤

* [连接群集](./connect-cluster.md)
