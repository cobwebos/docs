---
title: 用于 Kubernetes 的 Azure 容器服务简介
description: 有了用于 Kubernetes 的 Azure 容器服务，即可在 Azure 上轻松部署和管理基于容器的应用程序。
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 5bfa445eb11ed8be608278d0b95249372f9976ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-container-service-aks-preview"></a>Azure 容器服务 (AKS) 预览版简介

Azure 容器服务 (AKS) 让创建、配置和管理预配置以运行容器化应用程序的虚拟机群集变得更简单。 通过此服务，用户可使用现有技能或利用不断增加的大量社区专业知识，在 Microsoft Azure 上部署和管理基于容器的应用程序。

使用 AKS 时，可利用 Azure 的企业级功能，并且仍可通过 Kubernetes 以及 Docker 映像格式保留应用程序的可移植性。

> [!IMPORTANT]
> Azure 容器服务 (AKS) 目前以**预览版**提供。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。
>

## <a name="managed-kubernetes-in-azure"></a>Azure 中的托管 Kubernetes

AKS 通过将大量管理工作量卸载到 Azure，来降低管理 Kubernetes 群集所产生的复杂性和操作开销。 作为一个托管 Kubernetes 服务，Azure 可以自动处理运行状况监视和维护等关键任务。 此外，客户只需支付群集中的代理节点费，而不需要支付主节点的费用。 作为托管 Kubernetes 服务，AKS 提供：

> [!div class="checklist"]
> * 自动化的 Kubernetes 版本升级和修补
> * 轻松的群集缩放
> * 自我修复的托管控制平面（主节点）
> * 成本节省 - 只需支付代理池节点的运行费

Azure 可处理 AKS 群集中的节点管理，因此我们不再需要手动执行许多任务，例如群集升级。 由于 Azure 可自动处理这些关键维护任务，AKS 不提供对群集的直接访问（例如使用 SSH）。

## <a name="using-azure-container-service-aks"></a>使用 Azure 容器服务 (AKS)
AKS 旨在通过使用当今客户中热门的开源工具和技术提供容器托管环境。 为此，我们公开标准 Kubernetes API 终结点。 通过使用这些标准终结点，可利用能够与 Kubernetes 群集通信的任何软件。 例如，可以选择 [kubectl][kubectl-overview]、[helm][helm] 或 [draft][draft]。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>使用 Azure 容器服务 (AKS) 创建 Kubernetes 群集
若要开始使用 AKS，可通过 [Azure CLI][aks-quickstart] 或门户部署 AKS 群集（在 Marketplace 中搜索“Azure 容器服务”）。 如果需要对 Azure 资源管理器模板进行更多控制的高级用户，可以使用开源的 [acs-engine][acs-engine] 项目来生成自己的自定义 Kubernetes 群集，然后通过 `az` CLI 进行部署。

### <a name="using-kubernetes"></a>使用 Kubernetes
Kubernetes 对容器化应用程序自动进行部署、扩展和管理。 它具有一组丰富的功能，包括：
* 自动装箱
* 自我修复
* 水平扩展
* 服务发现和负载均衡
* 自动推出和回退
* 机密和配置管理
* 存储业务流程
* Batch 执行

## <a name="videos"></a>视频

Azure 容器服务 (AKS) - Azure Friday，2017 年 10 月：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

用于在 Kubernetes 上开发和部署应用程序的工具 - Azure OpenDev，2017 年 6 月：

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

学习 AKS 快速入门，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 教程][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

