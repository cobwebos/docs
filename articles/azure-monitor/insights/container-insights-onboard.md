---
title: 如何启用容器 Azure Monitor |Microsoft Docs
description: 本文介绍如何为容器启用和配置 Azure Monitor，以便你可以了解容器的执行情况以及已确定的与性能相关的问题。
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198065"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何启用容器 Azure Monitor

本文概述了可用于设置 Azure Monitor 容器的选项，这些选项可用于监视部署到 Kubernetes 环境并在上托管的工作负荷的性能：

- [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/)（AKS）

- 使用[AKS 引擎](https://github.com/Azure/aks-engine)在 Azure 上托管的自我托管的 Kubernetes 群集。

- 使用 AKS 引擎在[Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910)或本地托管的自我托管的 Kubernetes 群集。

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

可以使用以下受支持的方法为新的或 Kubernetes 的一个或多个现有部署启用容器 Azure Monitor：

- 从 Azure 门户、Azure PowerShell 或 Azure CLI

- 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

- **Log Analytics 工作区。**

    容器 Azure Monitor 支持 Azure[产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)）中列出的区域中的 Log Analytics 工作区。

    你可以在启用新 AKS 群集的监视时创建工作区，或让载入体验在 AKS 群集订阅的默认资源组中创建默认工作区。 如果选择自行创建工作区，可以通过 [Azure 资源管理器](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或在 [Azure 门户](../learn/quick-create-workspace.md)来创建。 有关用于默认工作区的支持映射对的列表，请参阅[容器 Azure Monitor 的区域映射](container-insights-region-mapping.md)。

- 你是**Log Analytics 参与者角色**的成员，用于启用容器监视。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../platform/manage-access.md)。

- 你是 AKS 群集资源上的 " **[所有者](../../role-based-access-control/built-in-roles.md#owner)** " 角色的成员。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 默认情况下，不收集 Prometheus 指标。 在[配置代理](container-insights-prometheus-integration.md)以收集它们之前，请务必查看 Prometheus[文档](https://prometheus.io/)，以了解可支持的擦除和方法。

## <a name="supported-configurations"></a>支持的配置

以下是容器 Azure Monitor 的正式支持。

- 环境： azure Red Hat OpenShift、Kubernetes 本地和 AKS Engine on Azure 和 Azure Stack。 有关详细信息，请参阅[Azure Stack 上的 AKS Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- 版本的 Kubernetes 和支持策略与[受支持的 AKS](../../aks/supported-kubernetes-versions.md)版本相同。 

## <a name="network-firewall-requirements"></a>网络防火墙要求

下表中的信息列出容器化代理与容器 Azure Monitor 进行通信所需的代理和防火墙配置信息。 来自代理的所有网络流量都是出站的，以便 Azure Monitor。

|代理资源|端口 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| \* .blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

下表中的信息列出了 Azure 中国区的代理和防火墙配置信息。

|代理资源|端口 |说明 | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | 数据引入 |
| *. oms.opinsights.azure.cn | 443 | OMS 载入 |
| \* .blob.core.windows.net | 443 | 用于监视出站连接。 |
| microsoft.com | 80 | 用于网络连接。 仅当代理映像版本为 ciprod09262019 或更早版本时，才需要执行此步骤。 |
| dc.services.visualstudio.com | 443 | 对于使用 Azure 公有云的代理遥测 Application Insights。 |

下表中的信息列出了 Azure 美国政府版的代理和防火墙配置信息。

|代理资源|端口 |说明 | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 数据引入 |
| *.oms.opinsights.azure.us | 443 | OMS 载入 |
| \* .blob.core.windows.net | 443 | 用于监视出站连接。 |
| microsoft.com | 80 | 用于网络连接。 仅当代理映像版本为 ciprod09262019 或更早版本时，才需要执行此步骤。 |
| dc.services.visualstudio.com | 443 | 对于使用 Azure 公有云 Application Insights 的代理遥测。 |

## <a name="components"></a>组件

监视性能的能力取决于专门为容器 Azure Monitor 开发的用于 Linux 的容器化 Log Analytics 代理。 此专用代理可从群集中的所有节点处收集性能和事件数据，并且在部署期间，会自动部署该代理，并注册指定 Log Analytics 工作区。 该代理的版本为 microsoft/oms:ciprod04202018 或更高版本，并由采用以下格式的日期表示：mmddyyyy。

>[!NOTE]
>使用 AKS 的 Windows Server 支持预览版，具有 Windows Server 节点的 AKS 群集没有安装代理来收集数据和转发到 Azure Monitor。 作为标准部署的一部分，自动部署到群集中的 Linux 节点会收集数据并将其转发到 Azure Monitor 代表群集中的所有 Windows 节点。  
>

当该代理的新版本发布时，会在承载于 Azure Kubernetes 服务 (AKS) 上的托管 Kubernetes 群集上自动升级该代理。 要关注发布的版本，请参阅[代理发布公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。

>[!NOTE]
>如果已部署 AKS 群集，可使用 Azure CLI 或提供的 Azure 资源管理器模板启用监视，如后文所示。 不能使用 `kubectl` 升级、删除、重新部署或部署代理。
>模板需要部署在群集所在的资源组中。

使用下表中所述的下列方法之一为容器启用 Azure Monitor。

| 部署状态 | 方法 | 说明 |
|------------------|--------|-------------|
| New AKS Kubernetes 群集 | [使用 Azure CLI 创建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 可以启用使用 Azure CLI 创建的新 AKS 群集的监视。 |
| | [使用 Terraform 创建 AKS 群集](container-insights-enable-new-cluster.md#enable-using-terraform)| 可以启用使用开源工具 Terraform 创建的新 AKS 群集的监视。 |
| | [使用 Azure 资源管理器模板创建 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 可以启用使用预先配置的 Azure 资源管理器模板创建的新 OpenShift 群集的监视。 |
| | [使用 Azure CLI 创建 OpenShift 群集](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | 可以在使用 Azure CLI 部署新的 OpenShift 群集时启用监视。 |
| 现有 AKS Kubernetes 群集 | [使用 Azure CLI 启用 AKS 群集](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 你可以使用 Azure CLI 启用已部署的 AKS 群集的监视。 |
| |[使用 Terraform 启用 AKS 群集](container-insights-enable-existing-clusters.md#enable-using-terraform) | 你可以启用对已使用开源工具 Terraform 部署的 AKS 群集的监视。 |
| | [为 Azure Monitor 启用 AKS 群集](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 你可以从 Azure Monitor 中的 "多群集" 页启用监视已部署的一个或多个 AKS 群集。 |
| | [从 AKS 群集启用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 可以直接从 Azure 门户中的 AKS 群集启用监视。 |
| | [使用 Azure 资源管理器模板启用 AKS 群集](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 可以使用预配置的 Azure 资源管理器模板启用监视 AKS 群集。 |
| | [启用混合 Kubernetes 群集](container-insights-hybrid-setup.md) | 你可以为托管在 Azure Stack 中的 AKS 引擎启用监视，也可以为本地托管的 Kubernetes 启用监视。 |
| | [使用 Azure 资源管理器模板启用 OpenShift 群集](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 可以使用预配置的 Azure 资源管理器模板启用对现有 OpenShift 群集的监视。 |
| | [为 Azure Monitor 启用 OpenShift 群集](container-insights-azure-redhat-setup.md#from-the-azure-portal) | 你可以从 Azure Monitor 中的 "多群集" 页启用监视已部署的一个或多个 OpenShift 群集。 |

## <a name="next-steps"></a>后续步骤

- 启用监视后，可以开始分析 Azure Kubernetes 服务（AKS）、Azure Stack 或其他环境中托管的 Kubernetes 群集的性能。 若要了解如何使用容器 Azure Monitor，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)。
