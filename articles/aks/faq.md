---
title: "Azure 容器服务常见问题解答"
description: "提供有关 Azure 容器服务的某些常见问题的解答。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 59dceded1e72e6e0e3d1a2bb25ca63bd023a9d21
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>有关 Azure 容器服务 (AKS) 的常见问题解答

此文章地址频繁问题有关 Azure 容器服务 (AKS)。

## <a name="which-azure-regions-provide-the-azure-container-service-aks-today"></a>哪些 Azure 区域现在可提供 Azure 容器服务 (AKS)？

- 加拿大中部 
- 加拿大东部 
- 美国中部 
- 美国东部 
- 东南亚 
- 欧洲西部 
- 美国西部 2 

## <a name="when-will-additional-regions-be-added"></a>何时将添加更多的地区？ 

添加更多的地区均为需求增加时。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？ 

Azure 可将安全修补程序在夜间自动应用于群集。 但是，你有责任确保节点根据需要进行重启。 执行节点重启具有多种选择：

- 通过 Azure 门户或 Azure CLI 手动执行。 
- 通过升级 AKS 群集。 群集将自动升级 [cordon 和 drain 节点](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)，然后使用最新的 Ubuntu 映像对其进行备份。 可以在节点上更新操作系统映像，而无需通过指定 `az aks upgrade` 中的当前群集版本来更改 Kubernetes 版本。
- 使用 [Kured](https://github.com/weaveworks/kured)，一种适用于 Kubernetes 的开放源代码重启守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重启的文件是否存在。 然后在群集中编排这些重启，方法是通过遵循前面介绍的相同 cordon 和 drain 过程。

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>是否建议客户使用 ACS 或 AKS？ 

虽然 AKS 仍处于预览阶段，但我们建议使用 ACS Kubernetes 或 [acs-engine](https://github.com/azure/acs-engine) 创建生产群集。 可将 AKS 用于概念证明部署和开发/测试环境。

## <a name="when-will-acs-be-deprecated"></a>何时弃用 ACS？ 

ACS 将弃用围绕 AKS 变得提供 GA 版。 必须将群集迁移到 AKS 该日期后的 12 个月。 在 12 个月期间，可以运行所有 ACS 操作。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？ 

节点自动缩放不支持，但在规划之中。 可能想要看一看这开源[自动缩放实现][auto-scaler]。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支持 Kubernetes 基于角色的访问控制 (RBAC)？

不支持，AKS 中当前不支持 RBAC，但不久将提供该支持。   

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

否，此操作尚不可用，但不久将会提供。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。 

否，但计划此集成。 在此期间，还可以尝试以下解决方案从[Hexadite][hexadite]。 

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

否，AKS 当前不提供基于 Windows Server 的代理节点，因此无法运行 Windows Server 容器。 如果需要在 Azure 中的 Kubernetes 上运行 Windows Server 容器，请参阅 [acs-engine 文档](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？ 

每个 AKS 部署都跨越两个资源组。 第一个由你创建，且仅包含 AKS 资源。 AKS 资源提供程序在部署期间将自动创建第二个资源组，且名称类似于 MC_myResourceGRoup_myAKSCluster_eastus。 第二个资源组包含与该群集相关联的所有基础结构资源，例如 VM、网络和存储。 创建第二个资源组可简化资源清理。 

如果要创建将与你的 AKS 群集配合使用的资源，例如存储帐户或保留公共 IP 地址，应将它们放在自动生成的资源组中。

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  