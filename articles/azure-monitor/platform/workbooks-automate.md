---
title: Azure 监视工作簿和 Azure 资源管理器模板
description: 通过通过 Azure 资源管理器模板部署的预构建和自定义参数化 Azure 监视器工作簿简化复杂报告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658398"
---
# <a name="programmatically-manage-workbooks"></a>以编程方式管理工作簿

资源所有者可以选择通过资源管理器模板以编程方式创建和管理其工作簿。 

这在以下情况下非常有用：
* 部署特定于组织或域的分析报告以及资源部署。 例如，您可以为新应用或虚拟机部署特定于组织的性能和故障工作簿。
* 使用现有资源的工作簿部署标准报表或仪表板。

工作簿将在所需的子/资源组中创建，并且使用资源管理器模板中指定的内容创建。

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>用于部署工作簿的 Azure 资源管理器模板
1. 打开要以编程方式部署的工作簿。
2. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
3. 使用工具栏上的_</>_ 按钮打开_高级编辑器_。
4. 在编辑器中，将_模板类型_切换到_资源管理器模板_。
5. 用于创建的资源管理器模板显示在编辑器中。 复制内容并按照"自如"使用，或将其与部署目标资源的大型模板合并。

    ![显示如何从工作簿 UI 中获取资源管理器模板的图像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>示例 Azure 资源管理器模板
此模板演示如何部署显示"你好世界！
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
| `workbookDisplayName` | 在库或保存列表中使用的工作簿的友好名称。 需要在资源组和源的范围内是唯一的 |
| `workbookType` | 工作簿下将显示的库。 支持的值包括工作簿、Azure`tsg`监视器等。 |
| `workbookSourceId` | 工作簿将关联到的资源实例的 ID。 新的工作簿将显示与此资源实例相关 ，例如在_工作簿_下的资源内容表中。 如果希望工作簿显示在 Azure 监视器的工作簿库中，请使用字符串_Azure 监视器_而不是资源 ID。 |
| `workbookId` | 此工作簿实例的唯一 guid。 使用 _[new Guid（）]_ 自动创建新 guid。 |
| `kind` | 用于指定创建的工作簿是共享的还是私有的。 将_共享_值用于共享工作簿，将_用户_共享为私有工作簿。 |
| `location` | 将创建工作簿的 Azure 位置。 使用 _[资源组（.位置]）_ 在资源组相同的位置创建它 |
| `serializedData` | 包含要在工作簿中使用的内容或有效负载。 使用工作簿 UI 中的资源管理器模板获取值 |

### <a name="workbook-types"></a>工作簿类型
工作簿类型指定新工作簿实例将在下显示的工作簿库类型。 选项包括：

| 类型 | 画廊位置 |
| :------------- |:-------------|
| `workbook` | 大多数报表中使用的默认值，包括应用程序见解的工作簿库、Azure 监视器等。  |
| `tsg` | 应用程序见解中的故障排除指南库 |
| `usage` | 应用程序洞察中_使用_下的_更多_库 |

### <a name="limitations"></a>限制
出于技术原因，此机制不能用于在应用程序见解_的工作簿_库中创建工作簿实例。 我们正在努力解决这一限制。 同时，我们建议您使用故障排除指南库（工作簿类型：）`tsg`来部署与应用程序见解相关的工作簿。

## <a name="next-steps"></a>后续步骤

了解如何使用工作簿为新的[Azure 监视器提供存储体验](../insights/storage-insights-overview.md)。

