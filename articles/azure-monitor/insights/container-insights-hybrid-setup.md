---
title: 使用 Azure 监视器配置用于容器的混合库伯奈斯群集 |微软文档
description: 本文介绍如何为容器配置 Azure 监视器，以监视托管在 Azure 堆栈或其他环境中的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198048"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>使用 Azure 监视器为容器配置混合库伯奈斯群集

容器的 Azure 监视器为 Azure 上的 Azure 库伯奈服务 （AKS） 和[AKS 引擎](https://github.com/Azure/aks-engine)提供了丰富的监视体验，这是托管在 Azure 上的自管理的 Kubernetes 群集。 本文介绍如何启用对 Azure 外部托管的 Kubernetes 群集的监视，并实现类似的监视体验。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

* Log Analytics 工作区。

    用于容器的 Azure Monitor 支持在 Azure [产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) 中列出的区域中的 Log Analytics 工作区。 要创建自己的工作区，可以通过[Azure 资源管理器](../platform/template-workspace-configuration.md)[、PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)或在[Azure 门户](../learn/quick-create-workspace.md)中创建它。

    >[!NOTE]
    >不支持将具有相同群集名称的多个群集监视到同一日志分析工作区。 群集名称必须是唯一的。
    >

* 您是**日志分析参与者角色**的成员，以启用容器监视。 有关如何控制对日志分析工作区的访问的详细信息，请参阅[管理对工作区和日志数据的访问](../platform/manage-access.md)

* [HELM 客户端](https://helm.sh/docs/using_helm/)将装载 Azure 监视器，用于指定 Kubernetes 群集的容器图表。

* Linux 的日志分析代理的容器化版本需要以下代理和防火墙配置信息才能与 Azure 监视器通信：

    |代理资源|端口 |
    |------|---------|   
    |*.ods.opinsights.azure.com |端口 443 |  
    |*.oms.opinsights.azure.com |端口 443 |  
    |* .blob.core.windows.net |端口 443 |  
    |*.dc.services.visualstudio.com |端口 443 |

* 容器化代理要求 Kubelet 的`cAdvisor secure port: 10250``unsecure port :10255`或要在群集中的所有节点上打开，以收集性能指标。 如果 Kubelet 的 cAdvisor 尚未配置，我们建议您在 Kubelet 的 cAdvisor 上进行配置`secure port: 10250`。

* 容器化代理要求在容器上指定以下环境变量，以便与群集中的 Kubernetes API 服务进行通信，以收集清单数据 -`KUBERNETES_SERVICE_HOST`和`KUBERNETES_PORT_443_TCP_PORT`。

>[!IMPORTANT]
>用于监视混合 Kubernetes 群集的最小代理版本是 ciprod10182019 或更高版本。

## <a name="supported-configurations"></a>支持的配置

用于容器的 Azure Monitor 正式支持以下内容。

- 环境：本地库伯奈斯、Azure 和 Azure 堆栈上的 AKS 引擎。 有关详细信息，请参阅 [Azure Stack 上的 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)。
- Kubernetes 和支持策略的版本与 [AKS 支持](../../aks/supported-kubernetes-versions.md)的版本相同。
- 容器运行时：码头和莫比
- 主节点和工作节点的 Linux 操作系统版本：Ubuntu （18.04 LTS 和 16.04 LTS）
- 支持访问控制：库伯奈斯 RBAC 和非 RBAC

## <a name="enable-monitoring"></a>启用监视

为混合 Kubernetes 群集的容器启用 Azure 监视器包括按顺序执行以下步骤。

1. 使用容器见解解决方案配置日志分析工作区。

2. 使用日志分析工作区为容器 HELM 图表启用 Azure 监视器。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>如何添加 Azure 监视器容器解决方案

可以使用 Azure PowerShell cmdlet`New-AzResourceGroupDeployment`或使用 Azure CLI 使用提供的 Azure 资源管理器模板部署解决方案。

如果不熟悉使用模板部署资源的概念，请参阅：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.59 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

此方法包含两个 JSON 模板。 一个模板指定用于启用监视的配置，另一个模板包含参数值，通过配置这些参数值可指定：

- **工作区 ResourceId** - 日志分析工作区的完整资源 ID。
- **工作区区域**- 工作区在其中创建的区域，在从 Azure 门户查看时，该区域也称为工作区属性中**的位置**。

要首先标识`workspaceResourceId`**容器SolutionParams.json**文件中参数值所需的日志分析工作区的完整资源 ID，请执行以下步骤，然后运行 PowerShell cmdlet 或 Azure CLI 命令以添加解决方案。

1. 使用以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    复制 **SubscriptionId** 的值。

2. 使用以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 以下示例以默认 JSON 格式显示订阅中的工作区列表。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在输出中，查找工作区名称，然后在字段 ID 下复制该日志分析工作区的完整资源**ID。**

4. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
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

5. 将此文件另存为容器Solution.json 到本地文件夹。

6. 将以下 JSON 语法粘贴到文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 使用步骤 3 中复制的值编辑**工作区 ResourceId**的值，并且**工作区区域**在运行 Azure CLI 命令 az[监视器日志分析工作区显示](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)后复制**区域**值。

8. 将此文件另存为容器SolutionParams.json 到本地文件夹。

9. 已做好部署此模板的准备。

   * 若要使用 Azure PowerShell 进行部署，请在包含模板的文件夹中使用以下命令：

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```powershell
       provisioningState       : Succeeded
       ```

   * 若要使用 Azure CLI 进行部署，请运行下列命令：

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```azurecli
       provisioningState       : Succeeded
       ```

       启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

## <a name="install-the-chart"></a>安装图表

要启用 HELM 图表，可以执行以下操作：

1. 通过运行以下命令将 Azure 图表存储库添加到本地列表：

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. 通过运行以下命令安装图表：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    如果日志分析工作区位于 Azure 中国中，请运行以下命令：

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    如果日志分析工作区位于 Azure 美国政府中，请运行以下命令：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>配置代理数据收集

使用图表版本 1.0.0，代理数据收集设置从 ConfigMap 进行控制。 请参阅[此处](container-insights-agent-config.md)有关代理数据收集设置的文档。

成功部署图表后，可以在 Azure 监视器中查看 Azure 监视器中混合 Kubernetes 群集的数据，查看 Azure 门户中的容器。  

>[!NOTE]
>从代理到在 Azure 日志分析工作区中提交，引入延迟大约需要 5 到 10 分钟。 群集的状态显示值 **"无数据**"或 **"未知"，** 直到 Azure 监视器中所有必需的监视数据都可用。

## <a name="troubleshooting"></a>疑难解答

如果在尝试为混合 Kubernetes 群集启用监视时遇到错误，请复制 PowerShell 脚本[TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1)并将其保存到计算机上的文件夹中。 此脚本用于帮助检测和修复遇到的问题。 它旨在检测和尝试更正的问题如下：

* 指定的日志分析工作区有效
* 日志分析工作区配置了容器的 Azure 监视器解决方案。 如果没有，请配置工作区。
* OmsAgent 副本集窗格正在运行
* OmsAgent 守护进程子窗格正在运行
* OmsAgent 运行状况服务正在运行
* 在容器化代理上配置的日志分析工作区 ID 和密钥与 Insight 配置的工作区匹配。
* 验证所有 Linux 工作节点`kubernetes.io/role=agent`都有用于计划 rs pod 的标签。 如果不存在，请添加它。
* 验证`cAdvisor secure port:10250`或`unsecure port: 10255`打开群集中的所有节点。

要使用 Azure PowerShell 执行，请使用包含脚本的文件夹中的以下命令：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>后续步骤

启用监视以收集混合 Kubernetes 群集的运行状况和资源利用率以及运行在它们的工作负载后，[了解如何将](container-insights-analyze.md)Azure 监视器用于容器。
