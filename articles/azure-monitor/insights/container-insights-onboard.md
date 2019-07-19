---
title: 如何启用容器 Azure Monitor |Microsoft Docs
description: 本文介绍如何为容器启用和配置 Azure Monitor，以便了解容器的性能以及已识别的性能相关问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867521"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何为容器启用 Azure Monitor

本文概述了可用于为容器设置 Azure Monitor 的选项，这些选项用于监视部署到 Kubernetes 环境并托管在 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/)上的工作负荷的性能。

可使用以下支持的方法为 AKS 的新部署，或是一个或多个现有部署启用适用于容器的 Azure Monitor：

* 借助 Azure 门户、Azure PowerShell 或 Azure CLI
* 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>系统必备
在开始之前，请确保做好以下准备：

* **Log Analytics 工作区。**

    容器 Azure Monitor 在 Azure[产品 (按区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)) 中列出的区域中支持 Log Analytics 工作区, 不包括区域**US Gov 弗吉尼亚州**。

    你可以在启用新 AKS 群集的监视时创建工作区, 或让载入体验在 AKS 群集订阅的默认资源组中创建默认工作区。 如果选择自行创建工作区，可以通过 [Azure 资源管理器](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或在 [Azure 门户](../learn/quick-create-workspace.md)来创建。 有关用于默认工作区的支持映射对的列表, 请参阅[容器 Azure Monitor 的区域映射](container-insights-region-mapping.md)。

* 需要成为 **Log Analytics 参与者角色**的成员才能启用容器监视。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../platform/manage-access.md)。

* 在 AKS 群集资源上，你是 **[所有者](../../role-based-access-control/built-in-roles.md#owner)** 角色的成员。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 默认情况下, 不收集 Prometheus 指标。 在[配置代理](container-insights-agent-config.md)以收集它们之前, 请务必查看 Prometheus[文档](https://prometheus.io/)以了解可以定义的内容。

## <a name="components"></a>组件

监视性能的能力依赖于 Linux 容器化 Log Analytics 代理，该代理专门针对用于容器的 Azure Monitor 开发而来。 此专用代理可从群集中的所有节点处收集性能和事件数据，并且在部署期间，会自动部署该代理，并注册指定 Log Analytics 工作区。 该代理的版本为 microsoft/oms:ciprod04202018 或更高版本，并由采用以下格式的日期表示：mmddyyyy  。

>[!NOTE]
>随着 Windows Server 预览版对 AKS 的支持，带有 Windows Server 节点的 AKS 群集没有安装代理即可收集数据并将数据转发到 Azure Monitor。 但是，在标准部署过程中，自动部署在群集中的 Linux 节点会代表群集中的所有 Windows 节点收集数据并将数据转发到 Azure Monitor。  
>

当该代理的新版本发布时，会在承载于 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集上自动升级该代理。 要关注发布的版本，请参阅[代理发布公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。

>[!NOTE]
>如果已部署 AKS 群集，可使用 Azure CLI 或提供的 Azure 资源管理器模板启用监视，如后文所示。 不能使用 `kubectl` 升级、删除、重新部署或部署代理。
>模板需要部署在群集所在的资源组中。

可以使用下表中所述的下列方法之一为容器启用 Azure Monitor。

| 部署状态 | 方法 | 描述 |
|------------------|--------|-------------|
| 新建 AKS 群集 | [使用 Azure CLI 创建群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 可以启用对使用 Azure CLI 创建的新 AKS 群集的监视。 |
| | [使用 Terraform 创建群集](container-insights-enable-new-cluster.md#enable-using-terraform)| 可以启用对使用开源工具 Terraform 创建的新 AKS 群集的监视。 |
| 现有 AKS 群集 | [使用 Azure CLI 启用](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 可以使用 Azure CLI 启用对已部署的 AKS 群集的监视。 |
| |[使用 Terraform 启用](container-insights-enable-existing-clusters.md#enable-using-terraform) | 可以使用开源工具 Terraform 启用对已部署的 AKS 群集的监视。 |
| | [从 Azure Monitor 启用](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 可以从 Azure Monitor 的 AKS 多群集页启用对一个或多个已经部署的 AKS 群集的监视。 |
| | [从 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 可以直接从 Azure 门户中的 AKS 群集启用监视。 |
| | [使用 Azure 资源管理器模板启用](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 可以使用预先配置的 Azure 资源管理器模板启用对 AKS 群集的监视。 |

## <a name="next-steps"></a>后续步骤

* 启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
