---
title: "在 Operations Management Suite (OMS) 中创建管理解决方案 | Microsoft 文档"
description: "通过提供客户可添加到其 OMS 工作区的打包管理方案，管理解决方案可扩展 Operations Management Suite (OMS) 的功能。  本文提供有关如何创建要在你自己的环境中使用或可供客户使用的管理解决方案的详细信息。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: fc8b76bf996060e226ac3f508a1ecffca6fc3c98
ms.openlocfilehash: caa2f96d452174ebb13c5cbf67737f20e2a2134d


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>在 Operations Management Suite (OMS) 中创建管理解决方案（预览版）
> [!NOTE]
> 这是在 OMS 中创建管理解决方案的初步文档，当前仅提供预览版。 如下所述的全部架构均会有变动。  
>
>

通过提供客户可添加到其 OMS 工作区的打包管理方案，管理解决方案可扩展 Operations Management Suite (OMS) 的功能。  本文提供了有关创建自己的管理解决方案的详细信息，你可以在自己的环境中使用该方案或通过社区提供给客户。

## <a name="planning-your-management-solution"></a>规划管理解决方案
OMS 中的管理解决方案包括支持特定管理方案的多个资源。  规划解决方案时，你应该侧重于尝试实现的方案和支持它的所有所需资源。  每个解决方案都应该自包含并定义所需的每个资源，即使一个或多个资源也由其他解决方案定义。  安装管理解决方案时，将创建每个资源（已存在的资源除外），你还可以定义删除解决方案时对资源的影响。  

例如，管理解决方案可能包含 [Azure 自动化 Runbook](../automation/automation-intro.md)（可使用[计划](../automation/automation-schedules.md)将数据收集到 Log Analytics 存储库）和[视图](../log-analytics/log-analytics-view-designer.md)（可提供所收集数据的各种可视化）。  其他解决方案可能也使用同一计划。  作为管理解决方案作者，你将总共定义三种资源，但要指定在删除解决方案时，Runbook 和视图也应自动删除。    你还将定义该计划，但指定如果在其他解决方案仍在使用该解决方案时将其删除，它应保留在原位。

## <a name="management-solution-files"></a>管理解决方案文件
管理解决方案作为[资源管理模板](../azure-resource-manager/resource-manager-template-walkthrough.md)实施。  了解如何创作管理解决方案的主要任务是了解如何[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)。  本文提供了用于解决方案的模板以及如何定义典型的解决方案资源的唯一详细信息。

管理解决方案文件的基本结构与如下所示的 [Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md#template-format) 相同。  以下各节描述了解决方案中的顶级元素及其内容。  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>parameters
[parameters](../azure-resource-manager/resource-group-authoring-templates.md#parameters) 是你在用户安装管理解决方案时从用户请求的值。  存在所有解决方案均具有的标准参数，你也可以根据特定解决方案的需要添加其他参数。  用户安装解决方案时提供参数值的方式取决于特定参数和解决方案安装方式。

用户通过 [Azure 应用商店](operations-management-suite-solutions.md#finding-and-installing-management-solutions)或 [Azure 快速入门模板](operations-management-suite-solutions.md#finding-and-installing-management-solutions) 安装管理解决方案时，系统会提示他们选择 [OMS 工作区和自动管理帐户](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)。  这些用于填充每个标准参数的值。  系统不提示用户直接提供标准参数的值，但会提示他们提供任何其他参数的值。

用户安装解决方案[另一种方法](operations-management-suite-solutions.md#finding-and-installing-management-solutions)时，必须为所有标准参数和所有其他参数提供一个值。

下面显示了一个示例参数。

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

下表描述了参数属性。

| 属性 | 说明 |
|:--- |:--- |
| type |参数的数据类型。 向用户显示的输入控件取决于数据类型。<br><br>bool - 下拉框<br>string - 文本框<br>int - 文本框<br>securestring - 密码字段<br> |
| category |参数的可选类别。  相同类别中的参数分到一组。 |
| control |字符串参数的其他功能。<br><br>datetime - 显示 Datetime 控件。<br>guid - 自动生成的 Guid 值，不显示参数。 |
| description |参数的可选说明。  显示在参数旁边信息气球。 |

### <a name="standard-parameters"></a>标准参数
下表列出了所有管理解决方案的标准参数。  从 Azure 应用商店或快速入门模板安装解决方案时，这些值用于为用户进行填充而不是提示他们。  如果使用其他方法安装解决方案，则用户必须为其提供值。

> [!NOTE]
> Azure 应用商店和快速入门模板中的用户界面正在等待表中的参数名称。  如果使用不同的参数名称，则将对用户进行提示，这些参数不会自动填充。
>
>

| 参数 | 类型 | 说明 |
|:--- |:--- |:--- |
| accountName |字符串 |Azure 自动化帐户名称。 |
| pricingTier |字符串 |Log Analytics 工作区和 Azure 自动化帐户的定价层。 |
| regionId |字符串 |Azure 自动化帐户的区域。 |
| solutionName |字符串 |解决方案名称。 |
| workspaceName |字符串 |Log Analytics 工作区名称。 |
| workspaceRegionId |字符串 |Log Analytics 工作区的区域。 |

### <a name="sample"></a>示例
下面是解决方案的示例参数实体。  这包括同一类别中的所有标准参数和两个其他参数。

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


请参阅解决方案的其他元素中语法为 **parameters('parameter name')** 的参数值。  例如，若要访问工作区名称，将使用 **parameters('workspaceName')**

## <a name="variables"></a>变量
**Variables** 元素包括将在管理解决方案的其余部分使用的值。  这些值不公开给安装解决方案的用户。  它们旨在向作者提供一个位置，供作者在该位置中管理可在整个解决方案中使用多次的值。 你应将任何特定于解决方案的值放在变量中，而不是在 **resources** 元素中对它们进行硬编码。  这使代码更具可读性，并允许在更高版本中轻松地更改这些值。

下面是具有在解决方案中使用的典型参数的 **variables** 元素示例。

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

请参阅整个解决方案中语法为 **variables('variable name')** 的变量值。  例如，若要访问 SolutionName 变量，将使用 **variables('solutionName')**

## <a name="resources"></a>资源
**resources** 元素定义管理解决方案中包括的不同资源。  这将是模板的最大且最复杂的部分。  使用以下结构定义资源。  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>依赖项
**dependsOn** 元素指定对另一个资源的[依赖](../azure-resource-manager/resource-group-define-dependencies.md)。  安装解决方案时，资源的所有依赖均已创建后才能创建资源。  例如，如果解决方案使用[作业资源](operations-management-suite-solutions-resources-automation.md#automation-jobs)安装，则该解决方案需要[启动 runbook](operations-management-suite-solutions-resources-automation.md#runbooks)。  作业资源将依赖于 runbook 资源，以确保在创建作业之前创建 runbook。

### <a name="oms-workspace-and-automation-account"></a>OMS 工作区和自动化帐户
管理解决方案需要 [OMS 工作区](../log-analytics/log-analytics-manage-access.md)来包含视图，也需要[自动化帐户](../automation/automation-security-overview.md#automation-account-overview)来包含 runbook 和相关资源。  这些内容在解决方案中的资源创建之前必须已经存在，并且不能在解决方案本身中定义。  部署解决方案时，用户将[指定工作区和帐户](operations-management-suite-solutions.md#oms-workspace-and-automation-account)，但作为作者，你应考虑以下几点。

## <a name="solution-resource"></a>解决方案资源
每个解决方案在 **resources** 元素中都需要资源项，用于定义解决方案本身。  该资源项的类型为 **Microsoft.OperationsManagement/solutions**。  下面是一个解决方案资源示例。  它的不同元素将在以下各节进行介绍。

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>解决方案名称
解决方案名称在 Azure 订阅中必须是唯一的。 要使用的建议值如下所示。  这将为基名称和 **workspaceName** 参数使用名为 **SolutionName** 的变量，以确保名称的唯一性。

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

这将解析为如下所示的名称。

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>依赖项
解决方案资源必须具有针对解决方案中所有其他资源的[依赖](../azure-resource-manager/resource-group-define-dependencies.md)，因为它们在解决方案创建之前必须已经存在。  通过在 **dependsOn** 元素中添加每个资源的项实现此目的。

### <a name="properties"></a>属性
解决方案资源具有下表中的属性。  这包括由用于定义安装解决方案后如何管理资源的解决方案引用和包含的资源。  解决方案中的每个资源应在 **referencedResources** 或 **containedResources** 属性中列出。

| 属性 | 说明 |
|:--- |:--- |
| workspaceResourceId |*<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>* 窗体中的 OMS 工作区的 ID。 |
| referencedResources |解决方案中不应随解决方案一起删除的资源的列表。 |
| containedResources |解决方案中应随解决方案一起删除的资源的列表。 |

上面的示例适用于具有 runbook、计划和视图的解决方案。  在 **properties** 元素中*引用* 计划和 runbook，这样它们就不会随解决方案一起删除。  *包含*视图，以便它将随解决方案一起删除。

### <a name="plan"></a>计划
解决方案资源的 **plan** 实体具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| name |解决方案名称。 |
| 版本 |由作者确定的解决方案版本。 |
| product |标识解决方案的唯一字符串。 |
| 发布者 |解决方案发布者。 |

## <a name="other-resources"></a>其他资源
可在以下文章中获取管理解决方案通用资源的详细信息和示例。

* [视图和仪表板](operations-management-suite-solutions-resources-views.md)
* [自动化资源](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>测试管理解决方案
在部署管理解决方案之前，建议你使用 [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy) 测试它。  这将验证你的解决方案文件，并帮助你在尝试部署它之前标识任何问题。

## <a name="next-steps"></a>后续步骤
* [已保存的搜索和警报](operations-management-suite-solutions-resources-searches-alerts.md)到管理解决方案。
* [将视图](operations-management-suite-solutions-resources-views.md)添加到管理解决方案。
* [将自动化 runbook 和其他资源添加](operations-management-suite-solutions-resources-automation.md)到管理解决方案。
* 了解[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)的详细信息。
* 在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates)中搜索不同 Resource Manager 模板的示例。



<!--HONumber=Jan17_HO4-->


