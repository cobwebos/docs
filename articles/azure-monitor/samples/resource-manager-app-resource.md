---
title: Application Insights 资源的资源管理器模板示例
description: 用于在 Azure Monitor 中部署 Application Insights 资源的 Azure 资源管理器模板示例。
ms.subservice: application-insights
ms.topic: sample
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/08/2020
ms.openlocfilehash: d7a8a7b7fa75d7d9bb4dc45a3432370bac4cb4d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498638"
---
# <a name="resource-manager-template-samples-for-creating-application-insights-resources"></a>用于创建 Application Insights 资源的资源管理器模板示例

本文包括 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)示例，用于部署和配置[经典 Application Insights 资源](../app/create-new-resource.md)和新的[基于工作区的 Application Insights 资源（预览）](../app/create-workspace-resource.md)。 每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="classic-application-insights-resource"></a>经典 Application Insights 资源

以下示例创建一个[经典 Application Insights 资源](../app/create-new-resource.md)。 

### <a name="template-file"></a>模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
          }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2014-08-01",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "my_app_insights_resource"
        },
        "regionId": {
            "value": "westus2"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "CustomDeployment"
        }
    }
}
```

## <a name="workspace-based-application-insights-resource"></a>基于工作区的 Application Insights 资源 

以下示例创建一个[基于工作区的 Application Insights 资源](../app/create-workspace-resource.md)。 基于工作区的 Application Insights 目前为预览版状态。 


### <a name="template-file"></a>模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "metadata": {
            "description": "Name of Application Insights resource."
          }
        },
        "type": {
            "type": "string",
            "metadata": {
            "description": "Type of app you are deploying. This field is for legacy reasons and will not impact the type of App Insights resource you deploy."
          }
        },
        "regionId": {
            "type": "string",
            "metadata": {
            "description": "Which Azure Region to deploy the resource to. This must be a valid Azure regionId."
          }
        },
        "tagsArray": {
            "type": "object",
            "metadata": {
            "description": "See documentation on tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources."
            }
        },
        "requestSource": {
            "type": "string",
            "metadata": {
            "description": "Source of Azure Resource Manager deployment"
        }
        },
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
            "description": "Log Analytics workspace ID to associate with your Application Insights resource."
        }
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "value": "my_workspace_based_resource"
    },
    "type": {
      "value": "web"
    },
    "regionId": {
      "value": "westus2"
    },
    "tagsArray": {
      "value": {}
    },
    "requestSource": {
      "value": "CustomDeployment"
    },
    "workspaceResourceId": {
      "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testxxxx/providers/microsoft.operationalinsights/workspaces/testworkspace"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* [获取 Azure Monitor 的其他示例模板](resource-manager-samples.md)。
* [详细了解经典 Application Insights 资源](../app/create-new-resource.md)。
* [详细了解基于工作区的 Application Insights 资源](../app/create-workspace-resource.md)。
