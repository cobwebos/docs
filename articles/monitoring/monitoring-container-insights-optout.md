---
title: 如何停止监视 Azure Kubernetes 服务群集 | Microsoft Docs
description: 本文介绍如何停止使用用于容器的 Azure Monitor 监视 Azure AKS 群集。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: magoedte
ms.openlocfilehash: 2b989fbebe237e4e3746ef2f237193587173dfe4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963400"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-azure-monitor-for-containers"></a>如何停止使用用于容器的 Azure Monitor 监视 Azure Kubernetes 服务 (AKS)

启用 AKS 群集监视后，如果决定不再监视它，可借助 PowerShell cmdlet **New-AzureRmResourceGroupDeployment** 或 Azure CLI 使用提供的 Azure 资源管理器模板*选择退出*。 一个 JSON 模板指定用于*选择退出*的配置。另一个 JSON 模板包含配置的参数值，可用于指定 AKS 群集资源 ID 和在其中部署群集的资源组。 

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="create-template"></a>创建模板

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
        }
      }
    }
    ```

4. 使用可在选定群集的“属性”页面上找到的 AKS 群集的值，编辑 **aksResourceId** 和 **aksResourceLocation** 的值。

    ![容器属性页面](./media/monitoring-container-health/container-properties-page.png)

    位于“属性”页时，还请复制“工作区资源 ID”。 如果决定稍后删除 Log Analytics 工作区，则需要此值。 不在此流程中执行删除 Log Analytics 工作区的操作。 

5. 将此文件以“OptOutParam.json”文件名保存到本地文件夹。
6. 已做好部署此模板的准备。 

## <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 删除解决方案
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

## <a name="remove-the-solution-using-powershell"></a>使用 PowerShell 删除解决方案

在包含模板的文件夹中执行以下 PowerShell 命令以删除解决方案并从 AKS 群集中清除配置。    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```powershell
ProvisioningState       : Succeeded
```

如果创建的工作区仅用于支持监视群集且不再被需要，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[使用 Azure 门户删除 Azure Log Analytics 工作区](../log-analytics/log-analytics-manage-del-workspace.md)。 不要忘记之前在步骤 4 中复制的“工作区资源 ID”，稍后将会需要。 

