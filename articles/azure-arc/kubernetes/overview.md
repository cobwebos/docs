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
ms.openlocfilehash: a4d53614a0ab6783b9adfe395fec4d79e7b62e1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858415"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>什么是已启用 Azure Arc 的 Kubernetes 预览版？

通过已启用 Azure Arc 的 Kubernetes 预览版，可以在 Azure 内部或外部附加和配置 Kubernetes 群集。 将 Kubernetes 群集附加到 Azure Arc 后，它将显示在 Azure 门户中。 它具有一个 Azure 资源管理器 ID 和一个托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。 

若要将 Kubernetes 群集连接到 Azure，群集管理员需要部署代理。 这些代理在名为 `azure-arc` 的 Kubernetes 命名空间中运行，并且是标准 Kubernetes 部署。 代理负责连接到 Azure、收集 Azure Arc 日志和指标并监视配置请求。 

已启用 Azure Arc 的 Kubernetes 支持行业标准 SSL 来保护传输中的数据。 此外，数据以静态加密方式存储在 Azure Cosmos DB 数据库中，确保数据保密性。
 
> [!NOTE]
> 启用了 Azure Arc 的 Kubernetes 处于预览状态。 建议不要将它用于生产工作负载。

## <a name="supported-kubernetes-distributions"></a>支持的 Kubernetes 分布

启用 Azure Arc 的 Kubernetes 可以与任何云本机计算基础 (CNCF) 认证的 Kubernetes 群集协同工作，例如 Azure 上的 AKS 引擎、Azure Stack Hub 上的 AKS 引擎、GKE、EKS 和 VMware vSphere 群集。

启用 Azure Arc 的 Kubernetes 功能已由 Arc 团队在以下分发版上进行了测试：
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS 引擎
* Azure Stack Hub 上的 AKS 引擎
* 群集 API 提供程序 Azure

## <a name="supported-scenarios"></a>支持的方案 

启用 Azure Arc 的 Kubernetes 支持以下方案： 

* 连接在 Azure 外部运行的 Kubernetes，以便用于库存、分组和标记。

* 使用基于 GitOps 的配置管理来部署应用程序并应用配置。 

* 使用适用于容器的 Azure Monitor 来查看和监视群集。 

* 使用适用于 Kubernetes 的 Azure Policy 来应用策略。 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>支持的区域 

以下区域目前支持启用 Azure Arc 的 Kubernetes： 

* 美国东部 
* 西欧

## <a name="frequently-asked-questions"></a>常见问题

* 已启用 Azure Arc 的 Kubernetes 和 Azure Kubernetes 服务 (AKS) 之间有何区别？

    Azure Kubernetes 服务 (AKS) 是 Azure 提供的托管 Kubernetes 服务。 可使用 AKS 在 Azure 中轻松地部署托管的 Kubernetes 群集。 AKS 通过将大量管理工作量卸载到 Azure，来降低管理 Kubernetes 所产生的复杂性和操作开销。 Kubernetes 主节点由 Azure 管理。 用户仅管理和维护代理节点。

    已启用 Azure Arc 的 Kubernetes 可用于将 Kubernetes 群集连接到 Azure，以便扩展 Azure 的管理功能（例如 Azure Monitor 和 Azure Policy）。 基础 Kubernetes 群集本身的维护由你完成。

* 是否需要将在 Azure 上运行的 Azure Kubernetes 服务群集连接到 Azure Arc？

    否。 已启用 Azure Arc 的 Kubernetes 的所有功能（如 Azure Monitor、Azure Policy (Gatekeeper)）都可通过 AKS（已在 Azure 中具有资源表示形式）本机可用。
    
* 是否应将 Azure Stack HCI 的 AKS 群集连接到 Azure Arc？ Azure Stack Hub 或 Azure Stack 引擎上运行的 Kubernetes 群集呢，连接它们会怎么样？

    是的，将这些群集连接到 Azure Arc 的确有好处。 它可在 Azure 资源管理器中提供这些 Kubernetes 群集的资源表示形式。 使用此资源表示形式，可以将群集配置、Azure Monitor、Azure Policy (Gatekeeper) 等功能扩展到这些 Kubernetes 群集

## <a name="next-steps"></a>后续步骤

* [连接群集](./connect-cluster.md)
