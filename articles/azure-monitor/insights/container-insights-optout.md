---
title: 如何停止监视 Azure Kubernetes 服务群集 | Microsoft Docs
description: 本文介绍如何停止使用用于容器的 Azure Monitor 监视 Azure AKS 群集。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: fe0155d6102dac12d5d4c01b78b1ddd45f9bee02
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382250"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>如何停止使用用于容器的 Azure Monitor 监视 Azure Kubernetes 服务 (AKS)

启用监视 AKS 群集后，如果决定不再监视它，可以停止监视群集。 本文介绍如何使用 Azure CLI 或提供的 Azure 资源管理器模板完成此操作。  


## <a name="azure-cli"></a>Azure CLI

使用 [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) 命令禁用容器的 Azure Monitor。 The command removes the agent from the cluster nodes, it does not remove the solution or the data already collected and stored in your Azure Monitor resource.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

若要重新启用对群集的监视，请参阅[使用 Azure CLI 启用监视](container-insights-enable-new-cluster.md#enable-using-azure-cli)。

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

下面提供了两个 Azure 资源管理器模板，以支持在资源组中一致且重复地删除解决方案资源。 一个是 JSON 模板，用于指定配置为“停止监视”，另一个模板包含配置的参数值，用于指定在其中部署群集的 AKS 群集资源 ID 和资源组。 

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>The template needs to be deployed in the same resource group of the cluster. 如果在使用此模板时省略任何其他属性或加载项，则可能导致从群集中删除这些属性或加载项。 For example, *enableRBAC* for RBAC policies implemented in your cluster, or *aksResourceTagValues* if tags are specified for the AKS cluster.  
>

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

### <a name="create-template"></a>创建模板

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
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. 将此文件以“OptOutTemplate.json”文件名保存到本地文件夹。

3. 将以下 JSON 语法粘贴到文件中：

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
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. 使用可在选定群集的“属性”页面上找到的 AKS 群集的值，编辑 **aksResourceId** 和 **aksResourceLocation** 的值。

    ![容器属性页面](media/container-insights-optout/container-properties-page.png)

    位于“属性”页时，还请复制“工作区资源 ID”。 如果决定稍后删除 Log Analytics 工作区，则需要此值。 不在此流程中执行删除 Log Analytics 工作区的操作。 

    Edit the values for **aksResourceTagValues** to match the existing tag values specified for the AKS cluster.

5. 将此文件以“OptOutParam.json”文件名保存到本地文件夹。

6. 已做好部署此模板的准备。 

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 删除解决方案

在 Linux 上使用 Azure CLI 执行以下命令以删除解决方案并清除 AKS 群集上的配置。

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>使用 PowerShell 删除解决方案

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在包含模板的文件夹中执行以下 PowerShell 命令以删除解决方案并从 AKS 群集中清除配置。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```powershell
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>后续步骤

如果创建的工作区仅用于支持监视群集且不再被需要，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[使用 Azure 门户删除 Azure Log Analytics 工作区](../../log-analytics/log-analytics-manage-del-workspace.md)。 Don't forget about the **Workspace Resource ID** copied earlier in step 4, you're going to need that. 

