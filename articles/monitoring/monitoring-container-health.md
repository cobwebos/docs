---
title: 监视 Azure Kubernetes 服务 (AKS) 运行状况（预览版）| Microsoft Docs
description: 本文介绍如何便捷地查看 AKS 容器的性能以快速了解托管 Kubernetes 环境的利用率。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069145"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>监视 Azure Kubernetes 服务 (AKS) 容器运行状况（预览版）

本文介绍如何设置和使用 Azure Monitor 容器运行状况来监视部署到 Azure Kubernetes 服务 (AKS) 托管的 Kubernetes 环境的工作负载性能。  监视 Kubernetes 群集和容器至关重要，特别是在使用多个应用程序大规模地运行生产群集时。

容器运行状况提供性能监视功能，用户可以从 Kubernetes 中提供的控制器、节点和容器以及指标 API 收集内存和处理器指标。  启用容器运行状况后，会使用适用于 Linux 的 OMS 代理的容器化版本自动收集这些指标并将指标存储在 [Log Analytics](../log-analytics/log-analytics-overview.md) 工作区中。  包含的预定义视图显示驻留容器的工作负载，以及影响 Kubernetes 群集性能运行状况的内容，以便了解：  

* 节点上运行的容器及其标识资源瓶颈的平均处理器和内存利用率
* 标识容器驻留在控制器和/或 Pod 中的位置，查看控制器或 Pod 的总体性能 
* 查看在与支持 Pod 的标准过程无关的主机上运行的工作负载的资源利用率
* 了解平均和最大负载下的群集行为，帮助标识容量需求并确定其可以承受的最大负载 

如果想要监视和管理 Docker 和 Windows 容器主机以查看配置、审核和资源利用率，请参阅[容器监视解决方案](../log-analytics/log-analytics-containers.md)。

## <a name="requirements"></a>要求 
开始前，请查看以下详细信息，了解受支持的必备组件。

- 新的或现有的 AKS 群集
- 适用于 Linux microsoft/oms:ciprod04202018 及更高版本的容器化 OMS 代理。 版本号通过 *mmddyyyy* 格式的日期表示。  它在容器运行状况载入期间自动安装。  
- Log Analytics 工作区。  它可以在启用新 AKS 群集的监视时创建，也可以通过 [Azure 资源管理器](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) 或从 [Azure 门户](../log-analytics/log-analytics-quick-create-workspace.md)创建。
- 成为 Log Analytics 参与者角色的成员，以便启用容器监视。  有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../log-analytics/log-analytics-manage-access.md)。

## <a name="components"></a>组件 

此功能依赖于适用于 Linux 的容器化 OMS 代理从群集的所有节点收集性能和事件数据。  启用容器监视后，自动部署代理并注册到指定的 Log Analytics 工作区。 

>[!NOTE] 
>如果已部署 AKS 群集，则使用提供的 Azure 资源管理器模板启用监视，如本文后面所示。 不能使用 `kubectl` 升级、删除、重新部署或部署代理。  
>

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>为每个新群集启用容器运行状况监视
从 Azure 门户部署时，可以启用对新 AKS 群集的监视。  按照快速入门文章[部署 Azure Kubernetes 服务 (AKS) 群集](../aks/kubernetes-walkthrough-portal.md)中的步骤操作。  位于“监视”页面时，为“启用监视”选项选择“是”以启用此功能，然后选择一个现有的 Log Analytics 工作区，或者创建一个新的。  

启用监视后，所有配置任务成功完成，可以使用以下两种方法监视群集性能：

1. 在左侧窗格选择“运行状况”，直接从 AKS 群集监视。<br><br> 
2. 单击选定群集的 AKS 群集页面中的“监视容器运行状况”标题。  在 Azure Monitor 中，从左侧窗格选择“运行状况”。  

![在 AKS 中选择容器运行状况的选项](./media/monitoring-container-health/container-performance-and-health-select-01.png)

启用监视后，可能需要大约 15 分钟才能查看群集的操作数据。  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>为现有托管群集启用容器运行状况监视
启用对已部署的 AKS 群集的监视时，可以通过 Azure 门户来完成，或者借助 PowerShell cmdlet **New-AzureRmResourceGroupDeployment** 或 Azure CLI 使用提供的 Azure 资源管理器模板来完成。  


### <a name="enable-from-azure-portal"></a>从 Azure 门户启用
执行以下步骤，从 Azure 门户启用对 AKS 容器的监视。

1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“容器”。 开始键入时，会根据输入筛选该列表。 选择“Kubernetes 服务”。<br><br> ![Azure 门户](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. 在容器列表中，选择一个容器。
3. 在容器概述页上，选择“监视容器运行状况”，会出现“载入到容器运行状况和日志”页。
4. 在“载入到容器运行状况和日志”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  该列表预先选择了 AKS 容器在订阅中部署到的默认工作区和位置。<br><br> ![启用 AKS 容器运行状况监视](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>若要创建新的 Log Analytics 工作区来存储群集中的监视数据，请执行[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)中的步骤，确保在 AKS 容器部署到的订阅中创建工作区。  
>
 
启用监视后，可能需要大约 15 分钟才能查看群集的操作数据。 

### <a name="enable-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用
此方法包含两个 JSON 模板，一个模板指定用于启用监视的配置，另一个 JSON 模板包含配置的参数值以指定以下内容：

* AKS 容器资源 ID 
* 部署群集的资源组 
* Log Analytics 工作区和要创建工作区的区域 

必须手动创建 Log Analytics 工作区。  若要创建工作区，可以通过 [Azure 资源管理器](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、从 [Azure 门户](../log-analytics/log-analytics-quick-create-workspace.md)创建一个。

如果不熟悉使用模板和 PowerShell 部署资源的概念，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)，对于 Azure CLI，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。  要求运行 Azure CLI 2.0.27 或更高版本。 运行 `az --version` 确定版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

#### <a name="create-and-execute-template"></a>创建和执行模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. 将此文件以“existingClusterOnboarding.json”文件名保存到本地文件夹。
3. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. 使用可以在 AKS 群集的“AKS 概述”页面上找到的值编辑“aksResourceId”和“aksResourceLocation”的值。  **workspaceResourceId** 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。  此外，为 **workspaceRegion** 指定工作区所在的位置。    
5. 将此文件以“existingClusterParam.json”文件名保存到本地文件夹。
6. 已做好部署此模板的准备。 

    * 请在包含模板的文件夹中使用以下 PowerShell 命令：

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        配置更改可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

        ```powershell
        provisioningState       : Succeeded
        ```

    * 借助 Linux 上的 Azure CLI 运行以下命令：
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        配置更改可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

        ```azurecli
        provisioningState       : Succeeded
        ```
启用监视后，可能需要大约 15 分钟才能查看群集的操作数据。  

## <a name="verify-agent-and-solution-deployment"></a>验证代理和解决方案部署
如果代理版本为 *06072018* 或更高，则可验证代理和解决方案是否均已成功部署。  如果是早期版本的代理，则只能验证代理部署情况。

### <a name="agent-version-06072018-and-higher"></a>代理 06072018 及更高版本
运行以下命令，验证代理是否已成功部署。   

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，表示它已正确部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要验证解决方案的部署，请运行以下命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

输出应如下所示，表示它已正确部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>代理版本低于 06072018

若要验证 *06072018* 之前发布的 OMS 代理版本是否已正确部署，请运行以下命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

输出应如下所示，表示它已正确部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>查看性能使用率
打开容器运行状况时，页面立即按整个群集显示性能使用率。  分四个视角查看有关 AKS 群集的信息：

- 群集
- Nodes 
- 控制器  
- 容器

在“群集”选项卡上，性能折线图显示群集的主要性能指标。  

![“群集”选项卡上的性能图表示例](./media/monitoring-container-health/container-health-cluster-perfview.png)

下面是呈现的性能指标的细分：

- 节点 CPU 利用率 (%) - 此图表表示整个群集的 CPU 利用率的聚合视角。  若要按时间范围筛选结果，可以通过图表上面的百分位选择器选择“平均”、“最小”、“最大”、“50%”、“90%”、“95%”（单选或复选均可）。 
- 节点内存利用率 (%) - 此图表表示整个群集的内存利用率的聚合视角。  若要按时间范围筛选结果，可以通过图表上面的百分位选择器选择“平均”、“最小”、“最大”、“50%”、“90%”、“95%”（单选或复选均可）。 
- 节点计数 - 此图表表示 Kubernetes 提供的节点计数和状态。  表示的群集节点的状态为“全部”、“就绪”和“未就绪”，可以通过图表上方的选择器单独筛选或以组合方式进行筛选。    
- 活动 Pod 计数 - 此图表表示 Kubernetes 提供的 Pod 计数和状态。  表示的 Pod 的状态为“全部”、“挂起”、“正在运行”和“未知”，可以通过图表上方的选择器单独筛选或以组合方式进行筛选。  

切换到“节点”选项卡时，行层次结构遵循以群集节点开头的 Kubernetes 对象模型。  展开节点，看到一个或多个 Pod 在节点上运行，如果每个 Pod 分到不止一个容器，则显示为层次结构中的最后一行。 还可以看到有多少非 Pod 相关工作负荷在主机上运行（如果主机存在处理器或内存使用压力）。

![性能视图中的示例 Kubernetes 节点层次结构](./media/monitoring-container-health/container-health-nodes-view.png)

可以从页面顶部选择控制器或容器，并查看这些对象的状态和资源使用率。  使用屏幕顶部的下拉框按命名空间、服务和节点进行筛选。 如果想要查看内存利用率，请从“指标”下拉列表中选择“内存 RSS”或“内存工作集”。  仅 Kubernetes 1.8 和更高版本支持“内存 RSS”。 否则，看到的“MIN%”值显示为“NaN%”，它表示未定义或无法表示的值的数值数据类型值。 

![容器节点性能视图](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

默认情况下，性能数据基于过去六个小时的数据，但是可以使用页面右上角的“时间范围”下拉列表更改窗口。 此时，页面不会自动刷新，因此需要手动刷新。 若要在时间范围内筛选结果，也可以通过百分位选择器选择“平均”、“最小”、“最大”、“50%”、“90%”、“95%”。 

![用于数据筛选的百分位选择](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

在以下示例中，请注意节点 *aks-nodepool-3977305*，其“容器”的值为 5，表示部署的容器数总和。

![单节点容器数汇总示例](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

它有助于快速确定群集节点间的容器是否适当均衡。  

下表介绍查看节点时显示的信息。

| 列 | Description | 
|--------|-------------|
| 名称 | 主机名称 |
| 状态 | 节点状态的 Kubernetes 视图 |
| AVG%、MIN%、MAX%、50%、90% | 基于所选时段百分位的平均节点百分比。 |
| AVG、MIN、MAX、50、90 | 基于所选时段的平均节点实际值。  对于节点，平均值从 CPU/内存限制集计算；对于 Pod 和容器，主机报告平均值。 |
| 容器 | 容器数量。 |
| 运行时间 | 表示节点启动或重启后的时间。 |
| 控制器 | 仅适用于容器和 Pod。 它显示驻留的控制器。 并非所有 Pod 都在控制器中，因此某些可能显示 N/A。 | 
| 趋势 AVG%、MIN%、MAX%、50%、90% | 显示控制器的百分位指标 (%) 的条形图趋势。 |


从选择器选择“控制器”。

![选择控制器视图](./media/monitoring-container-health/container-health-controllers-tab.png)

可以看到控制器的性能运行状况。

![<名称>控制器性能视图](./media/monitoring-container-health/container-health-controllers-view.png)

行层次结构从控制器开始，展开控制器，可以看到一个或多个容器。  展开 Pod，最后一行显示分组到 Pod 的容器。  

下表介绍查看控制器时显示的信息。

| 列 | Description | 
|--------|-------------|
| 名称 | 控制器名称|
| 状态 | 完成运行状态时容器的汇总状态，例如“正常”、“已终止”、“已失败”、“已停止”或“已暂停”。 如果容器仍在运行，但是状态未正确显示或者未被代理选择并且超出 30 分钟后仍未响应，则状态为“未知”。 下表中提供了状态图标的更多详细信息。|
| AVG%、MIN%、MAX%、50%、90% | 选中指标和百分位的每个实体的平均 % 汇总。 |
| AVG、MIN、MAX、50、90  | 所选百分位的容器的平均 CPU millicore 或内存性能汇总。  从 Pod 的 CPU/内存限制集计算平均值。 |
| 容器 | 控制器或 Pod 的容器总数。 |
| 重启数 | 容器重启计数汇总。 |
| 运行时间 | 表示容器启动后的时间。 |
| 节点 | 仅适用于容器和 Pod。 它显示驻留的控制器。 | 
| 趋势 AVG%、MIN%、MAX%、50%、90%| 表示控制器的百分位指标的条形图趋势。 |

状态字段中的图标指示容器的联机状态：
 
| 图标 | 状态 | 
|--------|-------------|
| ![“就绪”运行状态图标](./media/monitoring-container-health/container-health-ready-icon.png) | 正在运行（就绪）|
| ![“正在等待”或“已暂停”状态图标](./media/monitoring-container-health/container-health-waiting-icon.png) | “正在等待”或“已暂停”|
| ![“上次报告正在运行”状态图标](./media/monitoring-container-health/container-health-grey-icon.png) | 上次报告正在运行但已超过 30 分钟未响应|
| ![“已终止”状态图标](./media/monitoring-container-health/container-health-green-icon.png) | 成功停止或无法停止|

状态图标显示的计数基于 Pod 所提供的内容。 它显示较差的两个状态。将鼠标悬停在状态上时，它显示容器中所有 Pod 的汇总状态。  如果没有就绪状态，状态值会显示 **(0)**。  

从选择器选择“容器”。

![选择容器视图](./media/monitoring-container-health/container-health-containers-tab.png)

我们可以看到容器的性能运行状况。

![<名称>控制器性能视图](./media/monitoring-container-health/container-health-containers-view.png)

下表介绍查看容器时显示的信息。

| 列 | Description | 
|--------|-------------|
| 名称 | 控制器名称|
| 状态 | 容器状态（如果有）。 下表中提供了状态图标的更多详细信息。|
| AVG%、MIN%、MAX%、50%、90% | 选中指标和百分位的每个实体的平均 % 汇总。 |
| AVG、MIN、MAX、50、90  | 所选百分位的容器的平均 CPU millicore 或内存性能汇总。  从 Pod 的 CPU/内存限制集计算平均值。 |
| Pod | Pod 驻留的容器。| 
| 节点 |  容器驻留的节点。 | 
| 重启数 | 表示容器启动后的时间。 |
| 运行时间 | 表示容器启动或重启后的时间。 |
| 趋势 AVG%、MIN%、MAX%、50%、90% | 表示容器的平均指标 % 的条形图趋势。 |

状态字段中的图标指示 Pod 的联机状态：
 
| 图标 | 状态 | 
|--------|-------------|
| ![“就绪”运行状态图标](./media/monitoring-container-health/container-health-ready-icon.png) | 正在运行（就绪）|
| ![“正在等待”或“已暂停”状态图标](./media/monitoring-container-health/container-health-waiting-icon.png) | “正在等待”或“已暂停”|
| ![“上次报告正在运行”状态图标](./media/monitoring-container-health/container-health-grey-icon.png) | 上次报告正在运行但已超过 30 分钟未响应|
| ![“已终止”状态图标](./media/monitoring-container-health/container-health-terminated-icon.png) | 成功停止或无法停止|
| ![“已失败”状态图标](./media/monitoring-container-health/container-health-failed-icon.png) | “已失败”状态 |

## <a name="container-data-collection-details"></a>容器数据收集详细信息
容器运行状况从容器主机和容器收集各种性能指标和日志数据。 每隔三分钟收集数据。

### <a name="container-records"></a>容器记录

下表显示了容器运行状况收集的记录以及日志搜索结果中显示的数据类型的示例。

| 数据类型 | 日志搜索中的数据类型 | 字段 |
| --- | --- | --- |
| 主机和容器的性能 | `Perf` | 计算机、ObjectName、CounterName（处理器时间百分比、磁盘读取 MB、磁盘写入 MB、内存使用 MB、网络接收字节数、网络发送字节数、处理器使用秒数、网络）、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器库存 | `ContainerInventory` | TimeGenerated、计算机、容器名称、ContainerHostname、映像、ImageTag、ContainerState、ExitCode、EnvironmentVar、命令、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像库存 | `ContainerImageInventory` | TimeGenerated、计算机、映像、ImageTag、ImageSize、VirtualSize、正在运行、暂停、停止、失败、SourceSystem、ImageID、TotalContainer |
| 容器日志 | `ContainerLog` | TimeGenerated、计算机、映像 ID、容器名称、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服务日志 | `ContainerServiceLog`  | TimeGenerated、计算机、TimeOfCommand、映像、命令、SourceSystem、ContainerID |
| 容器节点清单 | `ContainerNodeInventory_CL`| TimeGenerated、计算机、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 容器进程 | `ContainerProcess_CL` | TimeGenerated、计算机、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes 群集中的 Pod 清单 | `KubePodInventory` | TimeGenerated、计算机、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes 群集节点部分清单 | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 事件 | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 群集中的服务 | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 群集节点部分的性能指标 | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName（cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、networkRxBytes、networkTxBytes、restartTimeEpoch、networkRxBytesPerSec、networkTxBytesPerSec、cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes）、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes 群集容器部分的性能指标 | Perf &#124; where ObjectName == “K8SContainer” | CounterName（cpuUsageNanoCores、memoryWorkingSetBytes、memoryRssBytes、restartTimeEpoch、cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryLimitBytes）、CounterValue、TimeGenerated、CounterPath、SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>搜索日志以分析数据
Log Analytics 有助于查找趋势、诊断瓶颈、预测或关联有助于确定是否最优执行当前群集配置的数据。  提供预定义日志搜索，可直接使用，也可自定义以根据自己的需要返回信息。 

通过选择展开控制器或容器时右侧提供的“查看日志”选项，可以在工作区执行数据交互式分析。  “日志搜索”位于所在的门户页面上。

![在 Log Analytics 中分析数据](./media/monitoring-container-health/container-health-view-logs.png)   

转发到 Log Analytics 的容器日志输出为 STDOUT 和 STDERR。 容器运行状况正在监视 Azure 托管的 Kubernetes (AKS)，由于生成了大量数据，今天不会收集 Kube-system。     

### <a name="example-log-search-queries"></a>日志搜索查询示例
从一或两个示例开始生成查询，并修改它们以适应需求，这通常很有用。 可以使用以下示例查询进行试验，帮助生成更高级的查询。

| 查询 | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命周期信息| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清单 | 
| **在高级分析中，选择折线图**：<br> 性能<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **在高级分析中，选择折线图**：<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器内存 |

## <a name="how-to-stop-monitoring-with-container-health"></a>如何停止监视容器运行状况
启用 AKS 容器监视之后，如果决定不再想要监视它，可以借助 PowerShell cmdlet New-AzureRmResourceGroupDeployment 或 Azure CLI 使用提供的 Azure 资源管理器模板选择退出。  一个 JSON 模板指定要选择退出的配置，另一个 JSON 模板包含配置用于指定部署群集的 AKS 群集资源 ID 和资源组的参数值。  如果不熟悉使用模板和 PowerShell 部署资源的概念，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)，对于 Azure CLI，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。  要求运行 Azure CLI 2.0.27 或更高版本。 运行 `az --version` 确定版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

### <a name="create-and-execute-template"></a>创建和执行模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. 将此文件以“OptOutTemplate.json”文件名保存到本地文件夹。
3. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. 使用可以在选中群集的“属性”页面上找到的 AKS 群集值编辑“aksResourceId”和“aksResourceLocation”的值。

    ![容器属性页面](./media/monitoring-container-health/container-properties-page.png)

    位于“属性”页时，还请复制“工作区资源 ID”。  如果决定稍后删除 Log Analytics 工作区（不在此进程中执行此操作），则需要此值。  

5. 将此文件以“OptOutParam.json”文件名保存到本地文件夹。
6. 已做好部署此模板的准备。 

    * 在包含模板的文件夹中使用以下 PowerShell 命令：

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        配置更改可能需要几分钟才能完成。 完成后，会看到一条包含返回的结果的消息，如下所示：

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * 借助 Linux 上的 Azure CLI 运行以下命令：

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        配置更改可能需要几分钟才能完成。 完成后，会看到一条包含返回的结果的消息，如下所示：

        ```azurecli
        ProvisioningState       : Succeeded
        ```

如果创建的工作区仅用于支持监视群集且不再被需要，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[使用 Azure 门户删除 Azure Log Analytics 工作区](../log-analytics/log-analytics-manage-del-workspace.md)。  不要忘记之前在步骤 4 中复制的“工作区资源 ID”，稍后将会需要。  

## <a name="troubleshooting"></a>故障排除
本节提供有助于排查容器运行状况问题的信息。

如果成功启用并配置容器，但在执行日志搜索时，Log Analytics 中看不到任何状态信息或结果，那么可以执行以下步骤帮助诊断问题。   

1. 通过运行以下命令检查代理状态： 

    `kubectl get ds omsagent --namespace=kube-system`

    输出应如下所示，表示它已正确部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 通过运行以下命令，检查代理版本为 *06072018* 或更高时的解决方案部署状态：

    `kubectl get deployment omsagent-rs -n=kube-system`

    输出应如下所示，表示它已正确部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. 通过运行以下命令检查 Pod 状态，验证它是否在运行：`kubectl get pods --namespace=kube-system`

    输出应如下所示，omsagent 状态为“正在运行”：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 检查代理日志。 部署容器化代理时，它通过运行 OMI 命令运行快速检查，并显示代理和 
5.  提供程序的版本。 若要查看代理是否成功载入，请运行以下命令：`kubectl logs omsagent-484hw --namespace=kube-system`

    状态应如下所示：

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>后续步骤

[搜索日志](../log-analytics/log-analytics-log-search.md)，查看详细的容器运行状况和应用程序性能信息。  