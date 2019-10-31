---
title: 以编程方式管理 Azure 资源管理器模板 Azure Monitor 工作簿 |Microsoft 文档
description: 利用通过 Azure 资源管理器模板部署的预生成的自定义参数化 Azure Monitor 工作簿，简化复杂报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166104"
---
# <a name="programmatically-manage-workbooks"></a>以编程方式管理工作簿

资源所有者可以选择通过资源管理器模板以编程方式创建和管理其工作簿。 

这在下列情况下非常有用：
* 部署组织或域特定的分析报表以及资源部署。 例如，你可以为新的应用或虚拟机部署特定于组织的性能和故障工作簿。
* 使用现有资源的工作簿部署标准报表或仪表板。

将在所需的子/资源组中创建工作簿，并在资源管理器模板中指定内容。

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>用于部署工作簿的 Azure 资源管理器模板
1. 打开要以编程方式部署的工作簿。
2. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏上的 " _</>_ " 按钮打开_高级编辑器_。
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
| `workbookType` | 工作簿将显示在其下的库。 支持的值包括工作簿、`tsg`、Azure Monitor 等。 |
| `workbookSourceId` | 工作簿将与之关联的资源实例的 ID。 新工作簿将显示与此资源实例相关的内容，例如，_工作簿_下的资源表中。 如果希望工作簿在 Azure Monitor 的工作簿库中显示，请使用字符串_Azure Monitor_而不是资源 ID。 |
| `workbookId` | 此工作簿实例的唯一 guid。 使用 _[newGuid （）]_ 自动创建新的 guid。 |
| `kind` | 用于指定创建的工作簿是共享的还是专用的。 使用共享工作簿的_共享_值和专用于专用工作簿的_用户_。 |
| `location` | 要在其中创建工作簿的 Azure 位置。 使用 _[resourceGroup （）. 位置]_ 在与资源组相同的位置中创建它 |
| `serializedData` | 包含要在工作簿中使用的内容或负载。 使用工作簿 UI 中的资源管理器模板获取值 |

### <a name="workbook-types"></a>工作簿类型
工作簿类型指定新工作簿实例将在其下显示的工作簿库类型。 选项包括：

| Type | 库位置 |
| :------------- |:-------------|
| `workbook` | 大多数报表中使用的默认值，包括 Application Insights、Azure Monitor 等的工作簿库。  |
| `tsg` | 疑难解答指南库中的 Application Insights |
| `usage` | Application Insights 中_使用_的库_越多_ |

### <a name="limitations"></a>限制
出于技术原因，此机制不能用于在 Application Insights 的_工作簿_库中创建工作簿实例。 我们正在努力解决此限制。 同时，我们建议使用故障排除指南库（workbookType： `tsg`）来部署 Application Insights 相关工作簿。

## <a name="next-steps"></a>后续步骤

了解如何使用工作簿为新[Azure Monitor 提供存储体验](../insights/storage-insights-overview.md)。

