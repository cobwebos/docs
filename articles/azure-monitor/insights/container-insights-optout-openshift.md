---
title: 如何停止监视 Azure 红帽开放移位群集 |微软文档
description: 本文介绍如何停止监视 Azure 红帽 OpenShift 群集，使用 Azure 监视器进行容器。
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250730"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>如何停止监视 Azure 红帽 OpenShift 群集，使用 Azure 监视器监视容器

启用 Azure 红帽 OpenShift 群集的监视后，如果您决定不再监视群集，则可以停止监视群集。 本文演示如何使用提供的 Azure 资源管理器模板实现此目的。  

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

下面提供了两个 Azure 资源管理器模板，以支持在资源组中一致且重复地删除解决方案资源。 一个是 JSON 模板，指定要停止监视的配置，另一个包含您配置为指定群集部署的 OpenShift 群集资源 ID 和 Azure 区域的参数值。

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 您必须运行 Azure CLI 版本 2.0.65 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="create-template"></a>创建模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. 将此文件以“OptOutTemplate.json”文件名保存到本地文件夹****。

3. 将以下 JSON 语法粘贴到文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. 使用 OpenShift 群集的值编辑**aroResourceId**和**aroResourceLocation 定位的值**，您可以在所选群集的 **"属性**"页上找到该值。

    ![容器属性页面](media/container-insights-optout-openshift/cluster-properties-page.png)

5. 将此文件以“OptOutParam.json”文件名保存到本地文件夹****。

6. 已做好部署此模板的准备。

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 删除解决方案

在 Linux 上使用 Azure CLI 执行以下命令，以删除解决方案并清理群集上的配置。

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>使用 PowerShell 删除解决方案

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在包含模板的文件夹中执行以下 PowerShell 命令，以删除解决方案并从群集中清除配置。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>后续步骤

如果创建的工作区仅用于支持监视群集且不再被需要，则需要手动删除它。 如果您不熟悉如何删除工作区，请参阅[删除 Azure 日志分析工作区](../../log-analytics/log-analytics-manage-del-workspace.md)。
