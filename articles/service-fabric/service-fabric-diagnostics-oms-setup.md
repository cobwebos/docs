---
title: Azure Service Fabric - 使用 OMS Log Analytics 设置监视 | Microsoft Docs
description: 了解如何设置 Operations Management Suite 来可视化和分析事件，进而监视 Azure Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/30/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 2589efa1808a394f2e32b842efa2ee70809da232
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-operations-management-suite-log-analytics-for-a-cluster"></a>为群集设置 Operations Management Suite Log Analytics

可通过 Azure 资源管理器、PowerShell 或 Azure Marketplace 设置 Operations Management Suite (OMS) 工作区。 如果要保留已更新的资源管理器部署模板供将来使用，请使用同一模板设置 OMS 环境。 如果部署的群集已启用诊断，则通过 Marketplace 部署更方便。 如果要将 OMS 部署到的帐户没有订阅级别访问权限，请通过 PowerShell 或资源管理器模板进行部署。

> [!NOTE]
> 要设置 OMS 来监视群集，需要启用诊断功能以监视群集级别或平台级别事件。

## <a name="deploy-oms-by-using-azure-marketplace"></a>使用 Azure Marketplace 部署 OMS

若要在部署群集之后添加 OMS 工作区，请转到在门户中的 Azure Marketplace，然后查找“Service Fabric 分析”：

1. 在左侧导航菜单中选择“新建”。 

2. 搜索“Service Fabric 分析”。 选择显示的资源。

3. 选择**创建**。

    ![Marketplace 中的 OMS SF 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. 在 Service Fabric 分析创建窗口中，为“OMS 工作区”字段选中“选择工作区”，然后选择“创建新工作区”。 填写所需的条目。 此处仅要求 Service Fabric 群集和 OMS 工作区的订阅相同。 验证条目后，即开始部署 OMS 工作区。 几分钟即可完成部署。

5. 完成后，在 Service Fabric 分析创建窗口底部再次选择“创建”。 请确保新工作区显示在 OMS 工作区下。 此操作会将解决方案添加到所创建的工作区中。

如果使用的是 Windows，请继续执行以下步骤，将 OMS 连接到群集事件所在的存储帐户。 

>[!NOTE]
>尚不可对 Linux 群集启用此体验。 

### <a name="connect-the-oms-workspace-to-your-cluster"></a>将 OMS 工作区连接到群集 

1. 需要将工作区连接到来自群集的诊断数据。 转到在其中创建 Service Fabric 分析解决方案的资源组。 选择 ServiceFabric\<nameOfOMSWorkspace\>然后转到其概述页。 可在此处更改解决方案设置和工作区设置，还可访问 OMS 门户。

2. 在左侧导航菜单的“工作区数据源”下，选择“存储帐户日志”。

3. 在“存储帐户日志”页面顶部选择“添加”，将群集的日志添加到工作区。

4. 选择“存储帐户”，添加在群集中创建的相应帐户。 若使用的是默认名称，存储帐户为 sfdg\<resourceGroupName\>。 还可检查 applicationDiagnosticsStorageAccountName 所使用的值，进而通过部署群集时所用的 Azure 资源管理器模板确认此名称。 如果不显示名称，请向下滚动并选择“加载更多”。 选择存储帐户名称。

5. 指定数据类型。 将其设置为“Service Fabric 事件”。

6. 确保源已自动设置为 WADServiceFabric\*EventTable。

7. 选择“确定”，将工作区连接到群集的日志。

    ![将存储帐户日志添加到 OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

该帐户现显示为工作区数据源中存储帐户日志的一部分。

已将 Service Fabric 分析解决方案添加到 OMS Log Analytics 工作区中，后者现已正确连接到群集的平台和应用程序日志表。 可以用相同的方式将其他源添加到工作区中。


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>使用资源管理器模板部署 OMS

使用资源管理器模板部署群集时，该模板还会新建一个 OMS 工作区，向其添加 Service Fabric 解决方案，并将其配置为读取相应存储表中的数据。

可使用[此示例模板](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms)，还可进行修改，使其满足你的要求。

请进行以下修改：
1. 将 `omsWorkspaceName` 和 `omsRegion` 添加到参数中，方法是将以下片段添加到 template.json 文件中定义的参数中。 适当时，自由选择修改默认值。 此外，再在 parameters.json 文件中添加两个新参数，以针对资源部署定义其值：
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` 值必须符合一组特定值。 选择离群集部署最近的值。

2. 如果要向 OMS 发送任意应用程序日志，请先确认模板中已包含 `applicationDiagnosticsStorageAccountType` 和 `applicationDiagnosticsStorageAccountName` 作为参数。 如果未包含，请将它们添加到变量部分，并按需编辑其值。 还可按照上述格式将其包含为参数。

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. 将 Service Fabric OMS 解决方案添加到模板的变量：

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. 将以下内容添加到资源部分的末尾，在 Service Fabric 群集资源的声明位置后面：

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > 如果已将 `applicationDiagnosticsStorageAccountName` 添加为变量，请确保将对其的每个引用修改为 `variables('applicationDiagnosticsStorageAccountName')` 而不是 `parameters('applicationDiagnosticsStorageAccountName')`。

5. 在 AzureRM PowerShell 模板中使用 `New-AzureRmResourceGroupDeployment` 将模板部署为要将资源管理器升级到群集。 示例命令如下：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure 资源管理器可检测出此命令要更新到现有资源。 它仅处理驱动现有部署的模板和提供的新模板之间的变化。

## <a name="deploy-oms-by-using-azure-powershell"></a>使用 Azure PowerShell 部署 OMS

还可使用 `New-AzureRmOperationalInsightsWorkspace` 命令通过 PowerShell 部署 OMS Log Analytics 资源。 要使用此方法，请确保已安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1)。 使用此脚本新建 OMS Log Analytics 工作区，并向其添加 Service Fabric 解决方案： 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

完成后，请按照上一部分中的步骤将 OMS Log Analytics 连接到相应的存储帐户。

还可添加其他解决方案或使用 PowerShell 对 OMS 工作区进行其他修改。 有关详细信息，请参阅 [使用 PowerShell 管理 Log Analytics](../log-analytics/log-analytics-powershell-workspace-configuration.md)。

## <a name="next-steps"></a>后续步骤
* [将 OMS 代理部署到节点上](service-fabric-diagnostics-oms-agent.md)，以收集性能计数器、docker 统计信息和容器日志
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* [使用视图设计器在 Log Analytics 中创建自定义视图](../log-analytics/log-analytics-view-designer.md)
