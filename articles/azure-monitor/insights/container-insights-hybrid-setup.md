---
title: 为混合 Kubernetes 群集配置适用于容器的 Azure Monitor | Microsoft Docs
description: 本文介绍了如何配置适用于容器的 Azure Monitor 以监视在 Azure Stack 或其他环境中承载的 Kubernetes 群集。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 26846148f3212699cecd6db3318cd2da2d9aa783
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398375"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>为混合 Kubernetes 群集配置适用于容器的 Azure Monitor

适用于容器的 Azure Monitor 针对 Azure Kubernetes 服务 (AKS) 和 [Azure 上的 AKS 引擎](https://github.com/Azure/aks-engine)（Azure 上承载的一个自我管理的 Kubernetes 群集）提供了丰富的监视体验。 本文介绍了如何对在 Azure 外部承载的 Kubernetes 群集启用监视，并实现类似的监视体验。

## <a name="supported-configurations"></a>支持的配置

适用于容器的 Azure Monitor 正式支持以下配置。

- 环境：

    - 本地 Kubernetes
    
    - Azure 和 Azure Stack 的 AKS 引擎。 有关详细信息，请参阅 [Azure Stack 上的 AKS 引擎](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
    
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 版本 4 及更高版本，位于本地或其他云环境中。

- Kubernetes 和支持策略的版本与 [AKS 支持](../../aks/supported-kubernetes-versions.md)的版本相同。

- 支持以下容器运行时：Docker、Moby 和 CRI 兼容的运行时，例如 CRI-O 和 ContainerD。

- 支持的适用于主节点和工作器节点的 Linux OS 版本包括：Ubuntu（18.04 LTS 和 16.04 LTS）和 Red Hat Enterprise Linux CoreOS 43.81。

- 支持的访问控制：Kubernetes RBAC 和非 RBAC

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

- [Log Analytics 工作区](../platform/design-logs-deployment.md)。

    用于容器的 Azure Monitor 支持在 Azure [产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) 中列出的区域中的 Log Analytics 工作区。 若要创建你自己的工作区，可通过 [Azure 资源管理器](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../learn/quick-create-workspace.md)进行创建。

    >[!NOTE]
    >不支持对同一 Log Analytics 工作区中具有相同群集名称的多个群集启用监视。 群集名称必须独一无二。
    >

- 需要成为 **Log Analytics 参与者角色**的成员才能启用容器监视。 要详细了解如何控制对 Log Analytics 工作区的访问，请参阅[管理对工作区和日志数据的访问](../platform/manage-access.md)。

- 若要查看监视数据，需要在 Log Analytics 工作区（该工作区为容器配置了 Azure Monitor）中拥有 [Log Analytics 读者](../platform/manage-access.md#manage-access-using-azure-permissions)角色。

- [HELM 客户端](https://helm.sh/docs/using_helm/)，用于为指定的 Kubernetes 群集载入适用于容器的 Azure Monitor 图表。

- 适用于 Linux 的 Log Analytics 代理的容器化版本与 Azure Monitor 进行通信需要以下代理和防火墙配置信息：

    |代理资源|端口 |
    |------|---------|
    |*.ods.opinsights.azure.com |端口 443 |
    |*.oms.opinsights.azure.com |端口 443 |
    |*.dc.services.visualstudio.com |端口 443 |

- 容器化代理要求在群集的所有节点上打开 Kubelet 的 `cAdvisor secure port: 10250` 或 `unsecure port :10255` 以收集性能指标。 建议你在 Kubelet 的 cAdvisor 上配置 `secure port: 10250`（如果尚未配置）。

- 容器化代理要求在容器上指定以下环境变量，以便与群集中的 Kubernetes API 服务通信以收集清单数据 - `KUBERNETES_SERVICE_HOST` 和 `KUBERNETES_PORT_443_TCP_PORT`。

>[!IMPORTANT]
>监视混合 Kubernetes 群集时支持使用的最低代理版本是 ciprod10182019 或更高版本。

## <a name="enable-monitoring"></a>启用监视

为混合 Kubernetes 群集启用适用于容器的 Azure Monitor 的操作包括按顺序执行以下步骤。

1. 为 Log Analytics 工作区配置容器见解解决方案。   

2. 通过 Log Analytics 工作区启用适用于容器的 Azure Monitor 的 HELM 图表。

有关中的监视解决方案的其他信息，请参阅 [此处](../../azure-monitor/insights/solutions.md)Azure Monitor。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>如何添加 Azure Monitor 容器解决方案

可以使用 Azure PowerShell cmdlet `New-AzResourceGroupDeployment` 或 Azure CLI，通过提供的 Azure 资源管理器模板来部署解决方案。

如果不熟悉使用模板部署资源的概念，请参阅：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.59 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

此方法包含两个 JSON 模板。 一个模板指定用于启用监视的配置，另一个模板包含参数值，通过配置这些参数值可指定：

- **workspaceResourceId** - Log Analytics 工作区的完整资源 ID。
- **workspaceRegion** - 在其中创建工作区的区域。当从 Azure 门户中查看时，它在工作区属性中也称作“位置”。

若要首先确定 **containerSolutionParams.json** 文件中的 `workspaceResourceId` 参数值所需的 Log Analytics 工作区的完整资源 ID，请执行以下步骤，然后运行 PowerShell cmdlet 或 Azure CLI 命令来添加解决方案。

1. 使用以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    复制 **SubscriptionId** 的值。

2. 使用以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 以下示例以默认 JSON 格式显示订阅中的工作区列表。

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在输出中，找到工作区名称，然后在字段 **ID** 下复制该 Log Analytics 工作区的完整资源 ID。

4. 将以下 JSON 语法复制并粘贴到该文件中：

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

5. 在一个本地文件夹中将该文件另存为 containerSolution.json。

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

7. 使用在第 3 步复制的值编辑 **workspaceResourceId** 的值。对于 **workspaceRegion**，请在运行 Azure CLI 命令 [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list) 后复制 **Region** 值。

8. 在一个本地文件夹中将该文件另存为 containerSolutionParams.json。

9. 已做好部署此模板的准备。

   - 若要使用 Azure PowerShell 进行部署，请在包含模板的文件夹中使用以下命令：

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```powershell
       provisioningState       : Succeeded
       ```

   - 若要使用 Azure CLI 进行部署，请运行下列命令：

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

       ```azurecli
       provisioningState       : Succeeded
       ```

       启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

## <a name="install-the-helm-chart"></a>安装 HELM 图表

本部分介绍如何为适用于容器的 Azure Monitor 安装容器化代理。 在继续之前，需要先确定 `omsagent.secret.wsid` 参数所需的工作区 ID 和 `omsagent.secret.key` 参数所需的主密钥。 可以通过执行以下步骤来确定这些信息，然后运行命令来安装使用 HELM 图表的代理。

1. 运行以下命令以确定工作区 ID：

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    在输出中，在“name”字段下找到工作区名称，然后在“customerID”下复制该 Log Analytics 工作区的工作区 ID 。

2. 运行以下命令以确定工作区的主密钥：

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    在输出中，在“primarySharedKey”下找到主密钥，然后复制其值。

>[!NOTE]
>以下命令仅适用于 Helm 版本 2。 `--name` 参数不适合在 Helm 版本 3 中使用。 

>[!NOTE]
>如果 Kubernetes 群集通过代理服务器进行通信，则使用代理服务器的 URL 来配置参数 `omsagent.proxy`。 如果群集不是通过代理服务器进行通信，则无需指定此参数。 有关详细信息，请参阅本文稍后的[配置代理终结点](#configure-proxy-endpoint)。

3. 通过运行以下命令，将 Azure 图表存储库添加到你的本地列表：

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

4. 运行以下命令来安装图表：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    如果 Log Analytics 的工作区位于 Azure 中国世纪互联，请运行以下命令：

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    如果 Log Analytics 工作区位于 Azure US Government 版中，请运行以下命令：

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>通过 API 模型启用 Helm 图表

你可以在 AKS 引擎群集规范 json 文件中指定一个加载项，也称为 API 模型。 在此加载项中，提供存储所收集监视数据的 Log Analytics 工作区的 base64 编码版 `WorkspaceGUID` 和 `WorkspaceKey`。 可以使用上一部分中的步骤 1 和 2 来查找 `WorkspaceGUID` 和 `WorkspaceKey`。

在此示例中，可以找到 Azure Stack Hub 群集支持的 API 定义 - [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)。 具体而言，请在 **kubernetesConfig** 中查找 **addons** 属性：

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>配置代理数据收集

从图表版本 1.0.0 开始，可通过 ConfigMap 控制代理数据收集设置。 有关代理数据收集设置的文档，请参阅[此文](container-insights-agent-config.md)。

成功部署图表后，可以在 Azure 门户中通过适用于容器的 Azure Monitor 查看混合 Kubernetes 群集的数据。  

>[!NOTE]
>从代理收集数据到在 Azure Log Analytics 工作区中提交数据，引入延迟大约为 5 到 10 分钟。 群集的状态将显示值“无数据”或“未知”，直到所有必需的监视数据在 Azure Monitor 中可用。

## <a name="configure-proxy-endpoint"></a>配置代理终结点

从图表版本 2.7.1 开始，图表将支持使用 `omsagent.proxy` 图表参数来指定代理终结点。 这样，图表即可通过代理服务器进行通信。 适用于容器的 Azure Monitor 代理与 Azure Monitor 之间的通信可以通过 HTTP 或 HTTPS 代理服务器进行，并且支持匿名身份验证和基本身份验证（用户名/密码）。

代理配置值具有以下语法：`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>如果代理服务器不需要身份验证，那么你仍需指定伪用户名/密码。 这可以是任何用户名或密码。

|属性| 说明 |
|--------|-------------|
|协议 | http 或 https |
|user | 用于代理身份验证的可选用户名 |
|password | 用于代理身份验证的可选密码 |
|proxyhost | 代理服务器的地址或 FQDN |
|port | 代理服务器的可选端口号 |

例如： `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

如果将协议指定为“http”，则使用 SSL/TLS 安全连接创建 HTTP 请求。 代理服务器必须支持 SSL/TLS 协议。

## <a name="troubleshooting"></a>故障排除

如果尝试为混合 Kubernetes 群集启用监视功能时遇到错误，请复制 PowerShell 脚本 [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1)，并将其保存到计算机上的某个文件夹中。 提供此脚本是为了帮助你检测和解决遇到的问题。 它可检测和尝试更正的问题如下所述：

- 指定的 Log Analytics 工作区有效
- 为 Log Analytics 工作区配置了适用于容器的 Azure Monitor 解决方案。 如果没有，请配置工作区。
- OmsAgent replicaset Pod 正在运行
- OmsAgent daemonset Pod 正在运行
- OmsAgent 运行状况服务正在运行
- 在容器化代理上配置的 Log Analytics 工作区 ID 和密钥与为见解配置的工作区匹配。
- 验证所有 Linux 工作器节点是否都通过 `kubernetes.io/role=agent` 标签来调度 rs Pod。 如果它不存在，请添加它。
- 验证是否已在群集中的所有节点上打开了 `cAdvisor secure port:10250` 或 `unsecure port: 10255`。

若要通过 Azure PowerShell 执行，请在包含脚本的文件夹中使用以下命令：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>后续步骤

启用监视功能以收集混合 Kubernetes 群集及其上运行的工作负荷的运行状况和资源利用率后，请了解[如何使用](container-insights-analyze.md)用于容器的 Azure Monitor。
