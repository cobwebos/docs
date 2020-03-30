---
title: 如何为容器启用 Azure Monitor | Microsoft Docs
description: 本文介绍如何为容器启用和配置 Azure Monitor，以便了解容器的性能以及已识别的性能相关问题。
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275303"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何为容器启用 Azure Monitor

本文概述了可用于为容器设置 Azure Monitor 的选项，这些选项用于监视部署到 Kubernetes 环境并托管在以下位置上的工作负荷的性能：

- [Azure 库伯奈斯服务](https://docs.microsoft.com/azure/aks/)（AKS）

- 使用[AKS 引擎](https://github.com/Azure/aks-engine)托管在 Azure 上的自管理的库伯奈斯群集。

- 使用 AKS 引擎托管在[Azure 堆栈](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或本地的自管理的 Kubernetes 群集。

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

可以使用以下支持的方法为容器的 Azure 监视器启用新的或多种 Kubernetes 的现有部署：

- 借助 Azure 门户、Azure PowerShell 或 Azure CLI

- 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

- **日志分析工作区。**

    用于容器的 Azure Monitor 支持在 Azure [产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) 中列出的区域中的 Log Analytics 工作区。

    可以在对新 AKS 群集启用监视时创建工作区，或者让加入体验在 AKS 群集订阅的默认资源组中创建默认的工作区。 如果选择自行创建工作区，可以通过 [Azure 资源管理器](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或在 [Azure 门户](../learn/quick-create-workspace.md)来创建。 有关用于默认工作区的受支持映射对的列表，请参阅[容器 Azure 监视器的区域映射](container-insights-region-mapping.md)。

- 您是**日志分析参与者角色**的成员，以启用容器监视。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../platform/manage-access.md)。

- 在 AKS 群集资源上，你是**[所有者](../../role-based-access-control/built-in-roles.md#owner)** 角色的成员。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 默认情况下不收集 Prometheus 指标。 [在配置代理](container-insights-prometheus-integration.md)收集它们之前，请务必查看 Prometheus[文档](https://prometheus.io/)，以了解可以刮擦的内容和支持的方法。

## <a name="supported-configurations"></a>支持的配置

用于容器的 Azure Monitor 正式支持以下内容。

- 环境：Azure 红帽 OpenShift、本地库伯内特和 Azure 堆栈上的 AKS 引擎。 有关详细信息，请参阅 [Azure Stack 上的 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- Kubernetes 和支持策略的版本与 [AKS 支持](../../aks/supported-kubernetes-versions.md)的版本相同。 

## <a name="network-firewall-requirements"></a>网络防火墙要求

下表中的信息列出了容器化代理与容器的 Azure 监视器通信所需的代理和防火墙配置信息。 从代理到 Azure 监视器的所有网络流量都来自外站。

|代理资源|端口 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| * .blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

下表中的信息列出了 Azure 中国区的代理和防火墙配置信息。

|代理资源|端口 |描述 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | 数据引入 |
| *.oms.opinsights.azure.cn | 443 | OMS 载入 |
| * .blob.core.windows.net | 443 | 用于监视出站连接。 |
| microsoft.com | 80 | 用于网络连接。 仅当代理映像版本为 ciprod09262019 或更低版本时，才需要此项。 |
| dc.services.visualstudio.com | 443 | 用于使用 Azure 公有云 Application Insights 进行代理遥测。 |

下表中的信息列出了 Azure 美国政府的代理和防火墙配置信息。

|代理资源|端口 |描述 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 数据引入 |
| *.oms.opinsights.azure.us | 443 | OMS 载入 |
| * .blob.core.windows.net | 443 | 用于监视出站连接。 |
| microsoft.com | 80 | 用于网络连接。 仅当代理映像版本为 ciprod09262019 或更低版本时，才需要此项。 |
| dc.services.visualstudio.com | 443 | 使用 Azure 公共云应用程序见解进行代理遥测。 |

## <a name="components"></a>组件

监视性能的能力依赖于专为容器 Azure 监视器开发的 Linux 的容器化日志分析代理。 此专用代理可从群集中的所有节点处收集性能和事件数据，并且在部署期间，会自动部署该代理，并注册指定 Log Analytics 工作区。 该代理的版本为 microsoft/oms:ciprod04202018 或更高版本，并由采用以下格式的日期表示：mmddyyyy**。

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
| 新的 AKS 库伯内斯群集 | [使用 Azure CLI 创建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 可以启用对使用 Azure CLI 创建的新 AKS 群集的监视。 |
| | [使用 Terraform 创建 AKS 群集](container-insights-enable-new-cluster.md#enable-using-terraform)| 可以启用对使用开源工具 Terraform 创建的新 AKS 群集的监视。 |
| | [使用 Azure 资源管理器模板创建 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 您可以启用监视使用预配置的 Azure 资源管理器模板创建的新 OpenShift 群集。 |
| | [使用 Azure CLI 创建打开移位群集](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 您可以使用 Azure CLI 部署新的 OpenShift 群集时启用监视。 |
| 现有 AKS 库伯奈斯群集 | [使用 Azure CLI 为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 可以使用 Azure CLI 启用对已部署的 AKS 群集的监视。 |
| |[使用 Terraform 为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-using-terraform) | 可以使用开源工具 Terraform 启用对已部署的 AKS 群集的监视。 |
| | [从 Azure 监视器为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以启用监视从 Azure 监视器中的多群集页面部署的一个或多个 AKS 群集。 |
| | [从 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 可以直接从 Azure 门户中的 AKS 群集启用监视。 |
| | [使用 Azure 资源管理器模板为 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 可以使用预先配置的 Azure 资源管理器模板启用对 AKS 群集的监视。 |
| | [启用混合库伯奈斯群集](container-insights-hybrid-setup.md) | 您可以启用监视托管在 Azure 堆栈中的 AKS 引擎，也可以监视本地托管的库伯内特。 |
| | [使用 Azure 资源管理器模板启用 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 您可以使用预先配置的 Azure 资源管理器模板启用监视现有 OpenShift 群集。 |
| | [启用 Azure 监视器的 OpenShift 群集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 您可以启用监视从 Azure 监视器中的多群集页面部署的一个或多个 OpenShift 群集。 |

## <a name="next-steps"></a>后续步骤

- 启用监视后，可以开始分析 Azure Kubernetes 服务 (AKS)、Azure Stack 或其他环境中托管的 Kubernetes 群集的性能。 若要了解如何使用用于容器的 Azure Monitor，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)。
