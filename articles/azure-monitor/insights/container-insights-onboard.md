---
title: 启用容器 Azure Monitor |Microsoft Docs
description: 本文介绍如何为容器启用和配置 Azure Monitor，使你了解容器的性能以及已识别的性能相关问题。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 69022643e3346444eee95f4487eeed292c4ef139
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994075"
---
# <a name="enable-azure-monitor-for-containers"></a>对容器启用 Azure Monitor

本文概述了可用于为容器设置 Azure Monitor 的选项，这些选项用于监视部署到 Kubernetes 环境并托管在以下位置上的工作负载的性能：

- [Azure Kubernetes 服务 (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 版本3.x 和4。x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 版本4。x  
- [启用了 Arc 的 Kubernetes 群集](../../azure-arc/kubernetes/overview.md)

你也可以监视部署到自托管 Kubernetes 群集的工作负载的性能，这些群集托管在以下位置上：
- Azure（通过使用 [AKS 引擎](https://github.com/Azure/aks-engine)）
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 或本地（通过使用 AKS 引擎）。

可使用以下支持的任意方法为 Kubernetes 的新部署或是一个/多个现有部署启用用于容器的 Azure Monitor：

- Azure 门户
- Azure PowerShell
- Azure CLI
- [Terraform 和 AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

首先，请确保你已满足以下要求：

- 拥有一个 Log Analytics 工作区。

   用于容器的 Azure Monitor 支持在 Azure [可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) 中列出的区域中的 Log Analytics 工作区。

   可以在为新 AKS 群集启用监视时创建工作区，或者可让加入体验在 AKS 群集订阅的默认资源组中创建默认的工作区。 
   
   如果你选择自己创建工作区，可通过以下方法创建工作区： 
   - [Azure Resource Manager](../samples/resource-manager-workspace.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure 门户](../learn/quick-create-workspace.md) 
   
   有关要用于默认工作区的支持的映射对的列表，请参阅 [容器 Azure Monitor 的区域映射](container-insights-region-mapping.md)。

- 需要成为 Log Analytics 参与者组的成员才能启用容器监视。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../platform/manage-access.md)。

- 需要成为 AKS 群集资源上[所有者](../../role-based-access-control/built-in-roles.md#owner)组的成员。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- 若要查看监视数据，需要在 Log Analytics 工作区（该工作区为容器配置了 Azure Monitor）中拥有 [Log Analytics 读者](../platform/manage-access.md#manage-access-using-azure-permissions)角色。

- 默认情况下不收集 Prometheus 指标。 在 [配置代理](container-insights-prometheus-integration.md) 以收集指标之前，请务必查看 [Prometheus 文档](https://prometheus.io/) ，以了解可以擦除哪些数据以及支持哪些方法。

## <a name="supported-configurations"></a>支持的配置

用于容器的 Azure Monitor 正式支持以下配置：

- 环境： azure Red Hat OpenShift、Kubernetes 本地和 Azure 上的 AKS 引擎，以及 Azure Stack。 有关详细信息，请参阅 [Azure Stack 上的 AKS 引擎](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- Kubernetes 和支持策略的版本与 [Azure Kubernetes 服务 (AKS) 中支持的版本](../../aks/supported-kubernetes-versions.md)相同。 

## <a name="network-firewall-requirements"></a>网络防火墙要求

下表列出容器化代理与 Azure Monitor 容器进行通信所需的代理和防火墙配置信息。 来自代理的所有网络流量都是出站的，以便 Azure Monitor。

|代理资源|端口 |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

下表列出了 Azure 中国世纪互联的代理和防火墙配置信息：

|代理资源|端口 |说明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | 数据引入 |
| `*.oms.opinsights.azure.cn` | 443 | OMS 载入 |
| `dc.services.visualstudio.com` | 443 | 用于使用 Azure 公有云 Application Insights 的代理遥测 |

下表列出了 Azure 美国政府版的代理和防火墙配置信息：

|代理资源|端口 |说明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | 数据引入 |
| `*.oms.opinsights.azure.us` | 443 | OMS 载入 |
| `dc.services.visualstudio.com` | 443 | 用于使用 Azure 公有云 Application Insights 的代理遥测 |

## <a name="components"></a>组件

监视性能的能力依赖于专门为用于容器的 Azure Monitor 开发的用于 Linux 的容器化 Log Analytics 代理。 此专用代理可从群集中的所有节点处收集性能和事件数据，并且在部署期间，会自动部署该代理，并注册指定 Log Analytics 工作区。 

该代理的版本为 microsoft/oms:ciprod04202018 或更高版本，并由采用以下格式的日期表示：mmddyyyy。

>[!NOTE]
>随着 Windows Server AKS 支持的正式发布，具有 Windows Server 节点的 AKS 群集在每个单独的 Windows Server 节点上安装了一个预览代理，作为一个 DaemonSet Pod 来收集日志并将其转发给 Log Analytics。 对于性能指标，在标准部署过程中，自动部署在群集中的 Linux 节点会代表群集中的所有 Windows 节点收集数据并将数据转发到 Azure Monitor。

当该代理的新版本发布时，在承载于 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集上自动升级该代理。 若要跟踪已发布的版本，请参阅[代理发布公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。

> [!NOTE]
> 如果你已部署 AKS 群集，可使用 Azure CLI 或提供的 Azure 资源管理器模板启用监视，如后文所示。 不能使用 `kubectl` 升级、删除、重新部署或部署代理。
>
> 模板需要部署在群集所在的资源组中。

若要为容器启用 Azure Monitor，请使用下表中所述的方法之一：

| 部署状态 | 方法 | 说明 |
|------------------|--------|-------------|
| 新建 Kubernetes 群集 | [使用 Azure CLI 创建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 可以为使用 Azure CLI 创建的新 AKS 群集启用监视。 |
| | [使用 Terraform 创建 AKS 群集](container-insights-enable-new-cluster.md#enable-using-terraform)| 可以为使用开源工具 Terraform 创建的新 AKS 群集启用监视。 |
| | [使用 Azure 资源管理器模板创建 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 你可以为使用预配置的 Azure 资源管理器模板创建的新 OpenShift 群集启用监视。 |
| | [使用 Azure CLI 创建 OpenShift 群集](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 可以在使用 Azure CLI 部署新的 OpenShift 群集时启用监视。 |
| 现有 Kubernetes 群集 | [使用 Azure CLI 启用对 AKS 群集的监视](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 可以使用 Azure CLI 为已部署的 AKS 群集启用监视。 |
| |[使用 Terraform 为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-using-terraform) | 可以使用开源工具 Terraform 为已部署的 AKS 群集启用监视。 |
| | [从 Azure Monitor 为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 可以从 Azure Monitor 的多群集页为一个或多个已部署的 AKS 群集启用监视。 |
| | [从 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 可以直接从 Azure 门户中的 AKS 群集启用监视。 |
| | [使用 Azure 资源管理器模板为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 可以使用预先配置的 Azure 资源管理器模板为 AKS 群集启用监视。 |
| | [为混合 Kubernetes 群集启用](container-insights-hybrid-setup.md) | 可以为托管在 Azure Stack 上的 AKS 引擎或为托管在本地的 Kubernetes 群集启用监视。 |
| | [启用启用 Arc 的 Kubernetes 群集](container-insights-enable-arc-enabled-clusters.md)。 | 你可以为托管在 Azure 外部并使用 Azure Arc 启用的 Kubernetes 群集启用监视。 |
| | [使用 Azure 资源管理器模板启用 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 可以使用预配置的 Azure 资源管理器模板为现有的 OpenShift 群集启用监视。 |
| | [为 Azure Monitor 启用 OpenShift 群集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 您可以为已从 Azure Monitor 中的 "multicluster" 页部署的一个或多个 OpenShift 群集启用监视。 |

## <a name="next-steps"></a>后续步骤

你现在已启用监视，接着可开始分析 Azure Kubernetes 服务 (AKS)、Azure Stack 或其他环境中托管的 Kubernetes 群集的性能。 若要了解如何使用用于容器的 Azure Monitor，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)。