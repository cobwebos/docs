---
title: 创建数据控制器
description: 在已部署的典型多节点 Kubernetes 群集上创建 Azure Arc 数据控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ba2d0acec37d0f59240381cdea04f4d53ded0b1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273035"
---
# <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器概述

可以使用多种不同的方法在多个不同类型的 Kubernetes 群集和托管的 Kubernetes 服务上创建支持 Azure Arc 的数据服务。

目前，支持的 Kubernetes 服务和分发的列表如下所示：

- Azure Kubernetes 服务 (AKS)
- Azure Kubernetes 服务引擎在 Azure Stack 上)  (AKS 引擎
- Azure Stack HCI 上的 Azure Kubernetes 服务
- Azure RedHat OpenShift (ARO)
- OpenShift 容器平台 (OCP) 
- AWS 弹性 Kubernetes 服务 (EKS)
- Google Cloud Kubernetes 引擎 (GKE) 
- 通常使用 kubeadm 部署的开源上游 Kubernetes

> [!IMPORTANT]
> * Kubernetes 支持的最低版本为 v 1.14。
> * 请参阅 [连接要求](connectivity.md) ，了解你的环境和 Azure 之间需要哪些连接。
> * 请参阅 [存储配置指南](storage-configuration.md) 以了解有关如何配置永久性存储的详细信息。
> * 如果你使用的是 Azure Kubernetes 服务，则群集的工作节点 VM 大小至少应为 **Standard_D8s_v3** 并使用 **高级磁盘。** 
> * 如果你使用的是其他 Kubernetes 分发或服务，则应确保你的最小节点大小为 8 GB RAM 和4个内核，以及所有 Kubernetes 节点上可用的 32 GB RAM 的总容量。 例如，你可能有1个位于 32 GB RAM 和4个内核的节点，或者可以有2个节点，每个节点都有一个 16 GB RAM 和4个内核。

> [!NOTE]
> 如果在 Azure 上使用 Red Hat OpenShift 容器平台，建议使用最新的可用版本。

根据你选择的选项，某些工具是 _必需_的，但建议在开始创建 Azure Arc 数据控制器之前 [安装所有客户端工具](./install-client-tools.md) 。

无论你选择哪种选项，在创建过程中，你都需要提供以下信息：

- **数据控制器名称** -数据控制器的描述性名称，如 "生产数据控制器"、"西雅图数据控制器"。
- **数据控制器用户名** -数据控制器管理员用户的任何用户名。
- **数据控制器密码** -数据控制器管理员用户的密码。
- **Kubernetes 命名空间的名称** -要在其中创建数据控制器的 Kubernetes 命名空间的名称。
- **连接模式** -群集的 [连接模式](./connectivity.md) 。 目前仅支持 "间接"。
- **Azure 订阅 ID** -你希望在 azure 中创建数据控制器资源的 AZURE 订阅 GUID。
- **Azure 资源组名称** -要在 Azure 中创建数据控制器资源的资源组的名称。
- **Azure location** -数据控制器资源元数据将存储在 azure 中的 azure 位置。 有关可用区域的列表，请参阅 [Azure 全球基础结构/产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)）。

## <a name="next-steps"></a>后续步骤

有多个选项可用于创建 Azure Arc 数据控制器：

> 想尝试一下吗？  
> 在 azure Kubernetes Service 上快速开始使用 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) (AKS) 、AWS 弹性 Kubernetes 服务 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或 Azure VM！
> 
- [使用 Azure 数据 CLI 创建数据控制器 (azdata) ](create-data-controller-using-azdata.md)
- [使用 Azure Data Studio 创建数据控制器](create-data-controller-azure-data-studio.md)
- [通过 Azure Data Studio 中的 Jupyter 笔记本创建 Azure 门户的数据控制器](create-data-controller-resource-in-azure-portal.md)
- [使用 Kubernetes 工具（如 kubectl 或 oc）创建数据控制器](create-data-controller-using-kubernetes-native-tools.md)
- [使用 Azure Arc Jumpstart 创建数据控制器以加速测试部署的体验](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)
