---
title: Azure Kubernetes 服务简介
description: 有了 Azure Kubernetes 服务，即可在 Azure 上轻松部署和管理基于容器的应用程序。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4b2294d7816a92dccb14caaadc09a2797edeafe6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237374"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS)

可以使用 Azure Kubernetes 服务 (AKS) 在 Azure 中轻松地部署托管的 Kubernetes 群集。 AKS 通过将大量管理工作量卸载到 Azure，来降低管理 Kubernetes 所产生的复杂性和操作开销。 作为一个托管 Kubernetes 服务，Azure 可以自动处理运行状况监视和维护等关键任务。 此外，此服务免费，你只需支付群集中的代理节点费，不需支付主节点的费用。

本文档概述了 Azure Kubernetes 服务 (AKS) 的功能。

## <a name="flexible-deployment-options"></a>灵活的部署选项

Azure Kubernetes 服务提供门户、命令行和模板驱动型部署选项（资源管理器模板和 Terraform）。 部署 AKS 群集时，会为你部署和配置 Kubernetes 主节点和所有节点。 另外，也可在部署过程中配置其他功能，例如高级网络、Azure Active Directory 集成、监视。

有关详细信息，请参阅 [AKS 门户快速入门][aks-portal]或 [AKS CLI 快速入门][aks-cli]。

## <a name="identity-and-security-management"></a>标识和安全管理

AKS 群集支持[基于角色的访问控制 (RBAC)][kubernetes-rbac]。 也可将 AKS 群集配置为与 Azure Active Directory 集成。 在此配置中，可以将 Kubernetes 访问权限配置为基于 Azure Active Directory 标识和组成员身份。

有关详细信息，请参阅[将 Azure Active Directory 与 AKS 集成][aks-aad]。

## <a name="integrated-logging-and-monitoring"></a>集成式日志记录和监视

可以从容器、节点和控制器收集内存和处理器指标，通过容器运行状况来显示性能。 容器日志也会收集。 此数据存储在 Log Analytics 工作区中，可以通过 Azure 门户、Azure CLI 或 REST 终结点获取。

有关详细信息，请参阅[监视 Azure Kubernetes 服务容器运行状况][container-health]。

## <a name="cluster-node-scaling"></a>群集节点缩放

资源需求增加时，可以相应地横向扩展 AKS 群集的节点。 如果资源需求下降，则可通过在群集中进行缩放来删除节点。 AKS 缩放操作可以通过 Azure 门户或 Azure CLI 来完成。

有关详细信息，请参阅[缩放 Azure Kubernetes 服务 (AKS) 群集][aks-scale]。

## <a name="cluster-node-upgrades"></a>群集节点升级

Azure Kubernetes 服务提供多个 Kubernetes 版本。 新版本在 AKS 中可用以后，即可使用 Azure 门户或 Azure CLI 升级群集。 在升级过程中，节点会被仔细封锁和排除以尽量减少对正在运行的应用程序造成中断。

有关详细信息，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集][aks-upgrade]。

## <a name="http-application-routing"></a>HTTP 应用程序路由

可以通过 HTTP 应用程序路由解决方案轻松地访问部署到 AKS 群集的应用程序。 启用后，HTTP 应用程序路由解决方案可以在 AKS 群集中配置入口控制器。 部署应用程序后，会自动配置可以公开访问的 DNS 名称。

有关详细信息，请参阅 [HTTP 应用程序路由][aks-http-routing]。

## <a name="gpu-enabled-nodes"></a>启用 GPU 的节点

AKS 支持创建启用了 GPU 的节点池。 Azure 目前提供单个或多个启用了 GPU 的 VM。 启用了 GPU 的 VM 是针对计算密集型、图形密集型和可视化工作负荷设计的。

有关详细信息，请参阅[使用 AKS 上的 GPU][aks-gpu]。

## <a name="development-tooling-integration"></a>开发工具集成

Kubernetes 有丰富的生态系统，其中包含各种开发和管理工具，例如 Helm、Draft 以及适用于 Visual Studio Code 的 Kubernetes 扩展。 这些工具可以与 Azure Kuberntees 服务无缝地配合使用。

另外，Azure Dev Spaces 为团队提供快速、迭代的 Kubernetes 开发体验。 只需最少的配置，即可直接在 Azure Kubernetes 服务 (AKS) 中运行和调试容器。

有关详细信息，请参阅 [Azure Dev Spaces][azure-dev-spaces]。

Azure DevOps 项目允许通过简单的解决方案将现有的代码和 Git 存储库带到 Azure 中。 DevOps 项目自动创建 Azure 资源（例如 AKS，VSTS 中的一种发布管道，其中包括用于 CI 的生成定义），为 CD 设置发布定义，然后创建适用于监视的 Azure Application Insights 资源。

有关详细信息，请参阅 [Azure DevOps 项目][azure-devops]。

## <a name="virtual-network-integration"></a>虚拟网络集成

AKS 群集可以部署到现有的 VNet 中。 在此配置中，群集中的每个 Pod 在 VNet 中分配有一个 IP 地址，并可直接与群集中的其他 Pod 以及 VNet 中的其他节点通信。 Pod 也可通过 ExpressRoute 和站点到站点 (S2S) VPN 连接与对等互连 VNet 中的其他服务和本地网络建立连接。

有关详细信息，请参阅 [AKS 网络概述][aks-networking]。

## <a name="private-container-registry"></a>专用容器注册表

集成 Azure 容器注册表 (ACR)，以便对 Docker 映像进行专用存储。

有关详细信息，请参阅 [Azure 容器注册表 (ACR)][acr-docs]。

## <a name="storage-volume-support"></a>存储卷支持

Azure Kubernetes 服务 (AKS) 支持为持久保存的数据装载存储卷。 AKS 群集在创建时会启用对 Azure 文件和 Azure 磁盘的支持。

有关详细信息，请参阅 [Azure 文件][azure-files]和 [Azure 磁盘][azure-disk]。

## <a name="docker-image-support"></a>Docker 映像支持

Azure Kubernetes 服务 (AKS) 支持 Docker 映像格式。

## <a name="kubernetes-certification"></a>Kubernetes 认证

Azure Kubernetes 服务 (AKS) 已被 CNCF 认证为符合 Kubernetes 规范。

## <a name="regulatory-compliance"></a>法规符合性

Azure Kubernetes 服务 (AKS) 符合 SOC 和 ISO/HIPPA/HITRUST 规范。

## <a name="next-steps"></a>后续步骤

学习 AKS 快速入门，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 快速入门][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

