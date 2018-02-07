---
title: "Azure Service Fabric - 使用 OMS Log Analytics 设置监视 | Microsoft Docs"
description: "了解如何设置 OMS 可视化和分析事件来监视 Azure Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>为群集设置 OMS Log Analytics

可以通过 Azure 资源管理器、PowerShell 或 Azure Marketplace 设置 OMS 工作区。 如果要保留已更新的资源管理器部署模板供将来使用，请使用同一模板设置 OMS 环境。 如果部署的群集已启用诊断，则通过 Marketplace 部署更方便。 如果要将 OMS 部署到的帐户没有订阅级别访问权限，请使用 PowerShell 或通过资源管理器模板部署。

> [!NOTE]
> 需要为群集启用诊断以查看群集/平台级事件，从而设置 OMS 以成功监视群集。

## <a name="deploying-oms-using-azure-marketplace"></a>使用 Azure Marketplace 部署 OMS

若要在部署群集之后添加 OMS 工作区，请转到 Azure Marketplace（在“门户”中），查找“Service Fabric 分析”。

1. 在左侧导航菜单中单击“新建”。 

2. 搜索“Service Fabric 分析”。 单击显示的资源。

3. 单击“创建”

    ![Marketplace 中的 OMS SF 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. 在 Service Fabric 分析创建窗口中，针对“OMS 工作区”字段单击“选择工作区”，然后单击“创建新工作区”。 填写必需的条目，此处仅要求 Service Fabric 群集和 OMS 工作区的订阅相同。 验证条目后，OMS 工作区将开始部署。 此过程应该只需要几分钟的时间。

5. 完成后，在 Service Fabric 分析创建窗口的底部再次单击“创建”。 请确保新工作区显示在 OMS 工作区下。 这会将解决方案添加到刚刚创建的工作区中。

如果使用的是 Windows，请继续执行以下步骤，将 OMS 挂接到存储群集事件的存储帐户。 正确为 Linux 群集启用此体验的操作仍在进行中。 同时，请继续将 OMS 代理添加到群集。  

1. 工作区仍需要连接到来自群集的诊断数据。 导航到在其中创建 Service Fabric 分析解决方案的资源组。 应该会看到 ServiceFabric(\<nameOfOMSWorkspace\>)。 单击该解决方案以导航到其概述页面，可以在此处更改解决方案设置、工作区设置，并导航到 OMS 门户。

2. 在左侧导航菜单上，单击“工作区数据源”下的“存储帐户日志”。

3. 在“存储帐户日志”页上，单击顶部的“添加”将群集的日志添加到工作区。

4. 单击“存储帐户”添加群集中创建的相应帐户。 如果使用了默认名称，则存储帐户名为 sfdg\<resourceGroupName\>。 还可以通过检查用于部署群集的 Azure 资源管理器模板，以及检查用于 `applicationDiagnosticsStorageAccountName` 的值来确认上述操作。 如果未显示名称，则可能还需要向下滚动并单击“加载更多”。 单击相应的存储帐户名称以选中它。

5. 接下来，将需要指定“数据类型”，应设为“Service Fabric 事件”。

6. 应将源自动设置为 WADServiceFabric\*EventTable。

7. 单击“确定”将工作区连接到群集的日志。

    ![将存储帐户日志添加到 OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

该帐户现在应显示为工作区数据源的存储帐户日志的一部分。

因此，现在已将 Service Fabric 分析解决方案添加到 OMS Log Analytics 工作区中，该工作区现已正确连接到群集的平台和应用程序日志表。 可以用相同的方式将其他源添加到工作区中。


## <a name="deploying-oms-using-a-resource-manager-template"></a>使用资源管理器模板部署 OMS

当使用资源管理器模板部署群集时，该模板应创建新的 OMS 工作区，向其添加 Service Fabric 解决方案，并将其配置为从适当的存储表中读取数据。

[此处](https://azure.microsoft.com/resources/templates/service-fabric-oms/)提供可以按需使用并修改的示例模板。 要获取更多提供不同 OMS 工作区设置选项的模板，请参阅 [Service Fabric 和 OMS 模板](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)。

所做主要更改如下：

1. 将 `omsWorkspaceName` 和 `omsRegion` 添加到参数。 即将以下片段添加到 template.json 文件中定义的参数。 适当时，自由选择修改默认值。 还应该将两个新参数添加到 parameters.json 中，以定义资源部署的值：
    
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

    `omsRegion` 值必须符合一组特定值。 应选择离群集部署最近的区域。

2. 如果要向 OMS 发送任意应用程序日志，请确认包含 `applicationDiagnosticsStorageAccountType` 和 `applicationDiagnosticsStorageAccountName` 作为模板中的参数。 如果未包含，请将它们添加到变量部分，并按需编辑其值。 如果愿意，还可以按照上面使用的格式包含他们作为参数。

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. 将 Service Fabric OMS 解决方案添加到模板的变量：

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. 将以下内容添加到资源部分的末尾，在声明 Service Fabric 群集资源之后的位置。

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

5. 将模板作为资源管理器升级部署到群集。 使用 AzureRM PowerShell 模块中的 `New-AzureRmResourceGroupDeployment` API 完成此操作。 示例命令如下：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure 资源管理器将能够检测到是要更新到现有资源。 它将仅处理驱动现有部署的模板和提供的新模板之间的变化。

## <a name="deploying-oms-using-azure-powershell"></a>使用 Azure PowerShell 部署 OMS

还可以通过 PowerShell 部署 OMS Log Analytics 资源。 这通过使用 `New-AzureRmOperationalInsightsWorkspace` 命令实现。 若要执行此操作，请确保已安装 [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1)。 使用此脚本新建 OMS Log Analytics 工作区，并向其添加 Service Fabric 解决方案： 

```ps

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

完成此操作后，如果群集是 Windows 群集，请按照上面部分中的步骤将 OMS Log Analytics 挂接到适当的存储帐户。

还可以添加其他解决方案或使用 PowerShell 对 OMS 工作区进行其他修改。 若要详细了解相关信息，请参阅[使用 PowerShell 管理 Log Analytics](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>后续步骤
* [将 OMS 代理部署到节点上](service-fabric-diagnostics-oms-agent.md)，以收集性能计数器、docker 统计信息和容器日志
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* [使用视图设计器在 Log Analytics 中创建自定义视图](../log-analytics/log-analytics-view-designer.md)
