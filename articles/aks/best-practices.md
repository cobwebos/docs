---
title: 适用于 Azure Kubernetes 服务 (AKS) 的最佳做法
description: 群集操作员和开发人员最佳做法的集合，用于在 Azure Kubernetes 服务 (AKS) 中生成并管理应用程序
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 12/07/2018
ms.author: iainfou
ms.openlocfilehash: 20922f0fb6932191b8e522e4640b1ba4fecd5ca4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514561"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>群集操作员和开发人员的最佳做法，用于在 Azure Kubernetes 服务 (AKS) 中生成并管理应用程序

若要在 Azure Kubernetes 服务 (AKS) 中成功地生成并运行应用程序，需要了解并实施一些关键的注意事项。 此方面的内容包括多租户和计划程序功能、群集和 Pod 安全性，或者业务连续性和灾难恢复。 以下最佳做法已分组，目的是帮助群集操作员和开发人员了解每个方面的注意事项，并实现相应的功能。

这些最佳做法和概念文章是与 AKS 产品组、工程团队和现场团队（包括全球黑带 (GBB)）一起共同撰写的。

## <a name="cluster-operator-best-practices"></a>群集操作员最佳做法

作为群集操作员，需要与应用程序所有者和开发人员一起协作，了解他们的需求， 然后就可以根据以下最佳做法来按需配置 AKS 群集。

**多租户**

* [群集隔离的最佳做法](operator-best-practices-cluster-isolation.md)
    * 包括可与命名空间配合使用的多租户核心组件和逻辑隔离。
* [有关基本计划程序功能的最佳做法](operator-best-practices-scheduler.md)
    * 包括资源配额和 Pod 中断预算。
* [有关高级计划程序功能的最佳做法](operator-best-practices-advanced-scheduler.md)
    * 包括使用排斥 (taint) 和容许 (toleration)、节点选择器和关联，以及 pod 间关联与反关联。
* [身份验证和授权的最佳做法](operator-best-practices-identity.md)
    * 包括与 Azure Active Directory 集成、使用基于角色的访问控制 (RBAC)，以及 Pod 标识。

**安全性**

* [有关群集安全性和升级的最佳做法](operator-best-practices-cluster-security.md)
    * 包括保护对 API 服务器的访问、限制容器访问权限，以及管理升级和节点重启。
* [容器映像管理和安全性的最佳做法](operator-best-practices-container-image-management.md)
    * 包括保护映像和运行时和在基本映像更新的自动的生成。
* [Pod 安全性的最佳做法](developer-best-practices-pod-security.md)
    * 包括保护对资源的访问、限制凭据公开，以及使用 Pod 标识和数字密钥保管库。

**网络和存储**

* [网络连接的最佳做法](operator-best-practices-network.md)
    * 包括使用不同的网络模型、使用入口和 Web 应用程序防火墙 (WAF)，以及保护节点 SSH 访问。
* [存储和备份的最佳做法](operator-best-practices-storage.md)
    * 包括选择适当的存储类型和节点大小、动态预配卷，以及数据备份。

**运行企业就绪型工作负荷**

* [业务连续性和灾难恢复的最佳做法](operator-best-practices-multi-region.md)
    * 包括使用区域对、通过 Azure 流量管理器管理多个群集，以及对容器映像进行异地复制。

## <a name="developer-best-practices"></a>开发人员最佳做法

作为开发人员或应用程序所有者，你可以简化开发体验并定义必要的应用程序性能需求。

* [应用程序开发人员资源管理最佳做法](developer-best-practices-resource-management.md)
    * 包括定义 Pod 资源请求和限制、配置开发工具，以及检查应用程序问题。
* [Pod 安全性的最佳做法](developer-best-practices-pod-security.md)
    * 包括保护对资源的访问、限制凭据公开，以及使用 Pod 标识和数字密钥保管库。

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS 概念

若要更好地了解这些最佳做法的特点和组件，也可查看以下概念性文章，了解 Azure Kubernetes 服务 (AKS) 中的群集：

* [Kubernetes 核心概念](concepts-clusters-workloads.md)
* [访问和标识](concepts-identity.md)
* [安全概念](concepts-security.md)
* [网络概念](concepts-network.md)
* [存储选项](concepts-storage.md)
* [缩放选项](concepts-scale.md)

## <a name="next-steps"></a>后续步骤

如果需要完成 AKS 入门，请按某个快速入门的说明操作，通过 [Azure CLI](kubernetes-walkthrough.md) 或 [Azure 门户](kubernetes-walkthrough-portal.md)来部署 Azure Kubernetes 服务 (AKS) 群集。
