---
title: Azure Monitor 工作簿和 Azure 资源管理器模板
description: 利用通过 Azure 资源管理器模板部署的预生成的自定义参数化 Azure Monitor 工作簿，简化复杂报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mbullwin
ms.openlocfilehash: 76ecc3ee17353ebd0bbead1bba959f85d521d0df
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982133"
---
# <a name="programmatically-manage-workbooks"></a>以编程方式管理工作簿

资源所有者可以选择通过资源管理器模板以编程方式创建和管理其工作簿。

这在下列情况下非常有用：
* 部署组织或域特定的分析报表以及资源部署。 例如，你可以为新的应用或虚拟机部署特定于组织的性能和故障工作簿。
* 使用现有资源的工作簿部署标准报表或仪表板。

将在所需的子/资源组中创建工作簿，并在资源管理器模板中指定内容。

可以通过编程方式管理两种类型的工作簿资源：
* [工作簿模板](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [工作簿实例](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>用于部署工作簿模板的 Azure 资源管理器模板

1. 打开要以编程方式部署的工作簿。
2. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏_Advanced Editor_上的_</>_ 按钮打开高级编辑器。
4. 确保位于 "_库模板_" 选项卡上。

    ![库模板选项卡](./media/workbooks-automate/gallery-template.png)
1. 将库模板中的 JSON 复制到剪贴板。
2. 下面是一个示例 Azure 资源管理器模板，用于将工作簿模板部署到 Azure Monitor 工作簿库。 粘贴复制的用于代替的`<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>`JSON。 可在[此处](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)找到用于创建工作簿模板的引用 Azure 资源管理器模板。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. 在`galleries`对象中，用您`name`的`category`值填充和键。 在下一部分中了解有关[参数](#parameters)的详细信息。
2. 使用[Azure 门户](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)、[命令行接口](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)、 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)等部署此 Azure 资源管理器模板。
3. 打开 Azure 门户，导航到在 Azure 资源管理器模板中选择的工作簿库。 在示例模板中，导航到 Azure Monitor 工作簿库：
    1. 打开 Azure 门户并导航到 Azure Monitor
    2. 从`Workbooks`目录中打开
    3. 在 "类别`Deployed Templates` " 下的库中查找模板（将是紫色项目之一）。

### <a name="parameters"></a>参数

|参数                |说明                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure 资源管理器中的工作簿模板资源的名称。                                  |
|`type`                    | 始终为 workbooktemplates/                                                            |
| `location`               | 将在其中创建工作簿的 Azure 位置。                                               |
| `apiVersion`             | 2019-10-17 预览                                                                                     |
| `type`                   | 始终为 workbooktemplates/                                                            |
| `galleries`              | 要在其中显示此工作簿模板的库集。                                                |
| `gallery.name`           | 库中工作簿模板的友好名称。                                             |
| `gallery.category`       | 要放置模板的库中的组。                                                     |
| `gallery.order`          | 确定在库的类别中显示模板的顺序的数字。 顺序越小，优先级越高。 |
| `gallery.resourceType`   | 与库对应的资源类型。 这通常是对应于资源的资源类型字符串（例如，microsoft.operationalinsights/工作区）。 |
|`gallery.type`            | 这是表示工作簿类型的唯一键，用于在资源类型中将库区分开来。 例如，Application Insights 具有类型`workbook`并`tsg`对应于不同的工作簿库。 |

### <a name="galleries"></a>库

| 库                                        | 资源类型                                      | 工作簿类型 |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Azure Monitor 中的工作簿                     | `Azure Monitor`                                    | `workbook`    |
| Azure Monitor 中的 VM Insights                   | `Azure Monitor`                                    | `vm-insights` |
| Log analytics 工作区中的工作簿           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Application Insights 中的工作簿              | `microsoft.insights/component`                     | `workbook`    |
| Application Insights 中的故障排除指南 | `microsoft.insights/component`                     | `tsg`         |
| Application Insights 中的用法                  | `microsoft.insights/component`                     | `usage`       |
| Kubernetes 服务中的工作簿                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| 资源组中的工作簿                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Azure Active Directory 中的工作簿            | `microsoft.aadiam/tenant`                          | `workbook`    |
| 虚拟机中的 VM Insights                | `microsoft.compute/virtualmachines`                | `insights`    |
| 虚拟机规模集中的 VM Insights      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>用于部署工作簿实例的 Azure 资源管理器模板

1. 打开要以编程方式部署的工作簿。
2. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏_Advanced Editor_上的_</>_ 按钮打开高级编辑器。
4. 在编辑器中，将_模板类型_切换为_资源管理器模板_。
5. 用于创建的资源管理器模板将显示在编辑器中。 复制内容并按原样使用，或将其与也部署目标资源的更大模板合并。

    ![显示如何从工作簿 UI 内获取资源管理器模板的图像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>示例 Azure 资源管理器模板
此模板演示如何部署显示 "Hello World！" 的简单工作簿
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>模板参数

| 参数 | 说明 |
| :------------- |:-------------|
| `workbookDisplayName` | 在库或已保存列表中使用的工作簿的友好名称。 需要在资源组和源的范围内是唯一的 |
| `workbookType` | 工作簿将显示在其下的库。 支持的值包括工作`tsg`簿、、Azure Monitor 等。 |
| `workbookSourceId` | 工作簿将与之关联的资源实例的 ID。 新工作簿将显示与此资源实例相关的内容，例如，_工作簿_下的资源表中。 如果希望工作簿在 Azure Monitor 的工作簿库中显示，请使用字符串_Azure Monitor_而不是资源 ID。 |
| `workbookId` | 此工作簿实例的唯一 guid。 使用 _[newGuid （）]_ 自动创建新的 guid。 |
| `kind` | 用于指定创建的工作簿是共享的还是专用的。 使用共享工作簿的_共享_值和专用于专用工作簿的_用户_。 |
| `location` | 要在其中创建工作簿的 Azure 位置。 使用 _[resourceGroup （）. 位置]_ 在与资源组相同的位置中创建它 |
| `serializedData` | 包含要在工作簿中使用的内容或负载。 使用工作簿 UI 中的资源管理器模板获取值 |

### <a name="workbook-types"></a>工作簿类型
工作簿类型指定新工作簿实例将在其下显示的工作簿库类型。 选项包括：

| 类型 | 库位置 |
| :------------- |:-------------|
| `workbook` | 大多数报表中使用的默认值，包括 Application Insights、Azure Monitor 等的工作簿库。  |
| `tsg` | 疑难解答指南库中的 Application Insights |
| `usage` | Application Insights 中_使用_的库_越多_ |

### <a name="limitations"></a>限制
出于技术原因，此机制不能用于在 Application Insights 的_工作簿_库中创建工作簿实例。 我们正在努力解决此限制。 同时，我们建议使用故障排除指南库（workbookType： `tsg`）来部署 Application Insights 相关工作簿。

## <a name="next-steps"></a>后续步骤

了解如何使用工作簿为新[Azure Monitor 提供存储体验](../insights/storage-insights-overview.md)。
