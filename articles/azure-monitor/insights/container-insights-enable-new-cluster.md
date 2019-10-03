---
title: 监视新的 Azure Kubernetes 服务 (AKS) 群集 |Microsoft Docs
description: 了解如何启用容器订阅与 Azure Monitor 监视新的 Azure Kubernetes 服务 (AKS) 群集。
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077525"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>启用新的 Azure Kubernetes 服务 (AKS) 群集的监视

本文介绍如何设置用于容器的 Azure Monitor 来监视托管的 Kubernetes 群集上托管[Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/)你准备用来部署你的订阅中。

你可以启用监视的 AKS 群集使用受支持的方法之一：

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>启用 Azure CLI

若要使用 Azure CLI 对新建的 AKS 群集启用监视，请遵循快速入门文章的[创建 AKS 群集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)部分中所述的步骤。  

>[!NOTE]
>如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.59 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
>

## <a name="enable-using-terraform"></a>使用 Terraform

如果正在[使用 Terraform 部署新的 AKS 群集](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)，请指定配置文件所需的参数，以便在未指定现有工作区时[创建 Log Analytics 工作区](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html)。 

>[!NOTE]
>如果选择使用 Terraform，则必须运行 Terraform Azure RM 提供程序 1.17.0 版或更高版本。

要将用于容器的 Azure Monitor 添加到工作区，请参阅 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)，并通过纳入 [addon_profile](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile)，以及指定 oms_agent，完成配置文件   。 

启用监视并成功完成所有配置任务后，可通过两种方法监视群集性能：

* 直接在 AKS 群集中从左侧窗格选择“运行状况”  。
* 在选定群集的 AKS 群集页面中选择“监视容器见解”磁贴  。 在 Azure Monitor 中，从左侧窗格选择“运行状况”  。 

  ![用于在 AKS 中为容器选择 Azure Monitor 的选项](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。 

## <a name="verify-agent-and-solution-deployment"></a>验证代理和解决方案部署
如果代理版本为 *06072018* 或更高版本，则可验证代理和解决方案是否均已成功部署。 如果是早期版本的代理，则只能验证代理的部署情况。

### <a name="agent-version-06072018-or-later"></a>06072018 版或更高版本的代理
运行以下命令，验证代理是否已成功部署。 

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，指明其已正确部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要验证解决方案的部署，请运行以下命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

输出应如下所示，指明其已正确部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>代理版本低于 06072018

若要验证 06072018  之前发布的 Log Analytics 代理版本是否已正确部署，请运行以下命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，指明其已正确部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 查看配置
使用 `aks show` 命令获取详细信息，例如，解决方案是否已启用、Log Analytics 工作区 resourceID 是什么，以及有关群集的摘要详细信息。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

片刻之后，该命令将会完成，并返回有关解决方案的 JSON 格式信息。  命令结果应显示监视加载项配置文件，并类似于以下示例输出：

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>后续步骤

* 如果在尝试载入解决方案时遇到问题，请查看[故障排除指南](container-insights-troubleshoot.md)

* 启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何将 Azure Monitor 用于容器，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
