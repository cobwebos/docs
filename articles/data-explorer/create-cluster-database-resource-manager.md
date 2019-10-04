---
title: 使用 Azure 资源管理器模板创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 Azure 资源管理器模板创建 Azure 数据资源管理器群集和数据库
author: orspod
ms.author: orspodek
ms.reviewer: oflipman
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: b8aa87b871b86a8dab3b1984b638366a42708483
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936894"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 模板](create-cluster-database-resource-manager.md)

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 

本文介绍如何使用[azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)创建 azure 数据资源管理器群集和数据库。 本文介绍如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。 有关创建模板的信息，请参阅[创作 Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-authoring-templates)。 有关在模板中使用的 JSON 语法和属性，请参阅[Kusto 资源类型](/azure/templates/microsoft.kusto/allversions)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>用于群集和数据库创建的 Azure 资源管理器模板

本文介绍如何使用[现有的快速入门模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07-preview",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

若要查找更多模板示例，请参阅 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)。

## <a name="deploy-the-template-and-verify-template-deployment"></a>部署模板并验证模板部署

可以通过[使用 Azure 门户](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment)或[使用 Powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment)来部署 Azure 资源管理器模板。

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>使用 Azure 门户部署模板并验证模板部署

1. 若要创建群集和数据库，请使用以下按钮启动部署。 右键单击并选择“在新窗口中打开”，以便按本文中的剩余步骤操作。

    [![部署到 Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-kusto-cluster-database/azuredeploy.json)

    “部署到 Azure”按钮将转到 Azure 门户以填写部署窗体。

    ![部署到 Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    您可以使用窗体[在 Azure 门户中编辑和部署模板](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template)。

1. 完整的**基本**信息和**设置**部分。 选择 "唯一群集" 和 "数据库名称"。
创建 Azure 数据资源管理器群集和数据库需要几分钟时间。

1. 若要验证部署，请在[Azure 门户](https://portal.azure.com)中打开资源组以查找新的群集和数据库。 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>使用 powershell 部署模板并验证模板部署

#### <a name="deploy-the-template-using-powershell"></a>使用 powershell 部署模板

1. 从以下代码块中选择“试用”，然后按照说明登录 Azure Cloud shell。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add(“clusters_kustocluster_name”, $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. 选择“复制”以复制 PowerShell 脚本。
1. 右键单击 shell 控制台，然后选择“粘贴”。
创建 Azure 数据资源管理器群集和数据库需要几分钟时间。

#### <a name="verify-the-deployment-using-powershell"></a>使用 PowerShell 验证部署

若要验证部署，请使用以下 Azure PowerShell 脚本。  如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行（读取主机）。 有关在 PowerShell 中管理 Azure 数据资源管理器资源的详细信息，请阅读[Az. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0)。 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 门户清理资源

按照[清理资源](create-cluster-database-portal.md#clean-up-resources)中的步骤操作，删除 Azure 门户中的资源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清理资源

如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行（读取主机）。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

[将数据引入 Azure 数据资源管理器群集和数据库](ingest-data-overview.md)
