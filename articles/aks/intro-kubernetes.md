---
title: "用于 Kubernetes 的 Azure 容器服务简介 | Microsoft Docs"
description: "有了用于 Kubernetes 的 Azure 容器服务，即可在 Azure 上轻松部署和管理基于容器的应用程序。"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, 容器, 微服务, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: fa46d16e2105c0354cc533c58fb9e0093c129ced
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Azure 容器服务 (AKS) 简介

Azure 容器服务 (AKS) 让创建、配置和管理预配置以运行容器化应用程序的虚拟机群集变得更简单。 通过此服务，用户可使用现有技能或利用不断增加的大量社区专业知识，在 Microsoft Azure 上部署和管理基于容器的应用程序。

使用 AKS 时，可利用 Azure 的企业级功能，并且仍可通过 Kubernetes 以及 Docker 映像格式保留应用程序的可移植性。

## <a name="using-azure-container-service-aks"></a>使用 Azure 容器服务 (AKS)
AKS 旨在通过使用当今客户中热门的开源工具和技术提供容器托管环境。 为此，我们公开标准 Kubernetes API 终结点。 通过使用这些标准终结点，可利用能够与 Kubernetes 群集通信的任何软件。 例如，可以选择 [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)、[helm](https://helm.sh/) 或 [draft](https://github.com/Azure/draft)。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>使用 Azure 容器服务 (AKS) 创建 Kubernetes 群集
若要开始使用 AKS，可通过 [Azure CLI](./kubernetes-walkthrough.md) 或门户（在 Marketplace 中搜索“Azure 容器服务”）部署 AKS 群集。 如果你是需要对 Azure Resource Manager 模板进行更多控制的高级用户，可以使用开源的 [acs-engine](https://github.com/Azure/acs-engine) 项目来生成自己的自定义 Kubernetes 群集，然后通过 `az` CLI 进行部署。

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

Azure 容器服务中的 Kubernetes 支持（Azure Friday，2017 年 1 月）：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

用于在 Kubernetes 上开发和部署应用程序的工具（Azure OpenDev，2017 年 6 月）：

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

学习 AKS 快速入门，了解有关部署和管理 AKS 的详细信息。

> [!div class="nextstepaction"]
> [AKS 教程](./kubernetes-walkthrough.md)