---
title: Azure Kubernetes 服务常见问题解答
description: 提供有关 Azure Kubernetes 服务的某些常见问题的解答。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: nepeters
ms.openlocfilehash: 5155d0c85e5b3698b0a13d2d5256a235858f0e82
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939757"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>哪些 Azure 区域现在可提供 Azure Kubernetes 服务 (AKS)？

- 澳大利亚东部
- 加拿大中部
- 加拿大东部
- 美国中部
- 美国东部
- 美国东部 2
- 北欧
- 英国南部
- 西欧
- 美国西部
- 美国西部 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 可将安全修补程序在夜间自动应用于群集。 但是，你有责任确保节点根据需要进行重启。 执行节点重启具有多种选择：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集将自动升级 [cordon 和 drain 节点](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)，然后使用最新的 Ubuntu 映像对其进行备份。 通过在 `az aks upgrade` 中指定当前群集版本，可更新节点上的 OS 映像，而不会更改 Kubernetes 版本。
- 使用 [Kured](https://github.com/weaveworks/kured)，一种适用于 Kubernetes 的开放源代码重启守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重启的文件是否存在。 然后在群集中编排重启，方法是通过遵循前面介绍的相同 cordon 和 drain 过程。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

支持，从 Kubernetes 1.10 开始，可以通过 [Kubernetes autoscaler][auto-scaler] 进行自动缩放。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支持 Kubernetes 基于角色的访问控制 (RBAC)？

支持，可以在从 Azure CLI 或 Azure 资源管理器模板部署 AKS 群集时启用 RBAC。 此功能很快就会出现在 Azure 门户中。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>AKS 支持哪些 Kubernetes 许可控制器？ 是否可以配置？

AKS 支持以下[许可控制器][admission-controllers]：

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook 
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

目前无法修改 AKS 中的许可控制器列表。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

可以，可以使用[高级网络功能](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md)将 AKS 群集部署到现有虚拟网络中。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

目前，AKS 未与 Azure Key Vault 本机集成。 但是，有社区解决方案，如[来自 Hexadite 的 acs-keyvault-agent][hexadite]。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

若要运行 Windows Server 容器，需要运行基于 Windows Server 的节点。 基于 Windows Server 的节点目前在 AKS 中不可用。 如果需要在 Azure 中的 Kubernetes 上运行 Windows Server 容器，请参阅 [acs-engine 文档](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

每个 AKS 部署都跨越两个资源组。 第一个资源组由你创建，且仅包含 Kubernetes 服务资源。 AKS 资源提供程序在部署期间自动创建第二个资源组，命名类似于 MC_myResourceGroup_myAKSCluster_eastus。 第二个资源组包含与该群集相关联的所有基础结构资源，例如 VM、网络和存储。 创建第二个资源组可简化资源清理。

如果要创建将与你的 AKS 群集配合使用的资源，例如存储帐户或保留公共 IP 地址，应将它们放在自动生成的资源组中。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议 (SLA) 中，提供商同意因未达到发布的服务级别向客户偿还服务费用。 由于 AKS 本身是免费的，没有费用需要偿还，因此也就没有正式的 SLA。 不过，我们会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
