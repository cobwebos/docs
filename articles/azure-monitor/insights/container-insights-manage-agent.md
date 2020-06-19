---
title: 如何管理用于容器的 Azure Monitor 代理 | Microsoft Docs
description: 本文介绍使用容器化 Log Analytics 代理来管理最常见的维护任务，该代理由用于容器的 Azure Monitor 使用。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: ce014d27c6acc473c4a435dfed4757fb0884f4fe
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652192"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>如何管理用于容器的 Azure Monitor 代理

用于容器的 Azure Monitor 使用适用于 Linux 的 Log Analytics 代理的容器化版本。 初始部署之后，在其生命周期内可能需要执行一些例程或可选任务。 本文详细介绍如何手动升级代理并禁用来自特定容器的环境变量集合。 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>如何升级用于容器的 Azure Monitor 代理

用于容器的 Azure Monitor 使用适用于 Linux 的 Log Analytics 代理的容器化版本。 发布该代理的新版本时，在 Azure Kubernetes 服务 (AKS) 和 Azure Red Hat OpenShift 版本 3.x 上承载的托管 Kubernetes 群集上，该代理会自动升级。 对于[混合 Kubernetes 群集](container-insights-hybrid-setup.md)和 Azure Red Hat OpenShift 版本 4.x，未托管该代理，需要手动升级该代理。

如果 AKS 或 Azure Red Hat OpenShift 版本 3.x 上托管的群集的代理升级失败，也可参阅本文，了解如何手动升级代理。 要关注发布的版本，请参阅[代理发布公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)。

### <a name="upgrade-agent-on-aks-cluster"></a>升级 AKS 群集上的代理

升级 AKS 群集上的代理的过程包括两个明确的步骤。 第一个步骤是使用 Azure CLI 禁止通过用于容器的 Azure Monitor 进行监视。 请遵循[禁用监视](container-insights-optout.md?#azure-cli)一文中介绍的步骤。 可以使用 Azure CLI 从群集的节点中删除代理，不会影响解决方案以及工作区中存储的相应数据。 

>[!NOTE]
>执行此维护活动时，群集中的节点不会转发所收集的数据，并且性能视图不会显示从删除代理到安装新版本这段时间内的数据。 
>

若要安装代理的新版本，请遵循[使用 Azure CLI 启用监视](container-insights-enable-new-cluster.md#enable-using-azure-cli)中介绍的步骤来完成此过程。  

重新启用监视后，可能需要约 15 分钟才能查看群集的更新后运行状况指标。 若要验证代理是否成功升级，请运行以下命令：`kubectl logs omsagent-484hw --namespace=kube-system`

状态应类似于以下示例，其中 omi 和 omsagent 的值应与[代理发布历史记录](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)中指定的最新版本相匹配 。  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>升级混合 Kubernetes 群集上的代理

执行以下步骤，升级在以下位置运行的 Kubernetes 群集上的代理：

* Azure 上托管的使用 AKS 引擎的自管理 Kubernetes 群集。
* Azure Stack 上或在本地托管的使用 AKS 引擎的自管理 Kubernetes 群集。
* Red Hat OpenShift 版本 4.x。

如果 Log Analytics 工作区位于商用 Azure 中，请运行以下命令：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

如果 Log Analytics 工作区位于 Azure 中国，请运行以下命令：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

如果 Log Analytics 工作区位于 Azure 美国政府，请运行以下命令：

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>升级 Azure Red Hat OpenShift v4 上的代理

执行以下步骤，升级在 Azure Red Hat OpenShift 版本 4.x 上运行的 Kubernetes 群集上的代理。 

>[!NOTE]
>Azure Red Hat OpenShift 版本 4.x 仅支持在 Azure 商业云中运行。
>

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<azureAroV4ResourceId> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>如何禁用容器上的环境变量集合

用于容器的 Azure Monitor 从在 pod 中运行的容器中收集环境变量，并将这些环境变量显示在“容器”视图中所选容器的属性窗格中。 可在部署 Kubernetes 集群期间或之后通过设置环境变量 AZMON_COLLECT_ENV 禁用特定容器的集合来控制此行为。 此功能可从代理版本 ciprod11292018 或更高版本获得。  

要禁用新容器或现有容器上的环境变量集合，请在 Kubernetes 部署 yaml 配置文件中将变量 AZMON_COLLECT_ENV 设置为“False”值 。 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

运行以下命令，将更改应用到除 Azure Red Hat OpenShift 以外的 Kubernetes 群集）：`kubectl apply -f  <path to yaml file>`。 若要编辑 ConfigMap 并将此更改应用于 Azure Red Hat OpenShift 群集，请运行以下命令：

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

这会打开默认文本编辑器。 设置该变量后，将文件保存在编辑器中。

要验证配置更改是否生效，请在用于容器的 Azure Monitor 中的“容器”视图中选择容器，并在属性面板中展开“环境变量” 。  此部分应只显示前面创建的变量 - AZMON_COLLECT_ENV=FALSE。 对于所有其他容器，环境变量部分应列出发现的所有环境变量。

要重新启用环境变量的发现，请应用与前面相同的过程，将值从“False”更改为“True”，然后重新运行 `kubectl` 命令来更新容器 。  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>后续步骤

如果升级代理时遇到问题，请查看[故障排除指南](container-insights-troubleshoot.md)来获得支持。
