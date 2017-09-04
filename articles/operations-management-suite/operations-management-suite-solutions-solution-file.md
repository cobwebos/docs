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
ms.date: 04/30/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: ee3462c13101d18921dc488b08c79e1e4e02ff3a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>在 Operations Management Suite (OMS) 中创建管理解决方案文件（预览版）
> [!NOTE]
> 这是在 OMS 中创建管理解决方案的初步文档，当前仅提供预览版。 如下所述的全部架构均会有变动。  

Operations Management Suite (OMS) 中的管理解决方案作为 [Resource Manager 模板](../azure-resource-manager/resource-manager-template-walkthrough.md)实现。  了解如何创作管理解决方案的主要任务是了解如何[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)。  本文提供了用于解决方案的模板以及如何配置典型的解决方案资源的唯一详细信息。


## <a name="tools"></a>工具

可使用任何文本编辑器处理解决方案文件，但我们建议利用 Visual Studio 或 Visual Studio Code 提供的功能，如以下文章中所述。

- [通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [在 Visual Studio Code 中使用 Azure Resource Manager 模板](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>结构
管理解决方案文件的基本结构与如下所示的 [Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md#template-format) 相同。  以下各节描述了解决方案中的顶级元素及其内容。  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>parameters
[parameters](../azure-resource-manager/resource-group-authoring-templates.md#parameters) 是你在用户安装管理解决方案时从用户请求的值。  存在所有解决方案均具有的标准参数，你也可以根据特定解决方案的需要添加其他参数。  用户安装解决方案时提供参数值的方式取决于特定参数和解决方案安装方式。

用户通过 [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) 或 [Azure 快速入门模板](operations-management-suite-solutions.md#finding-and-installing-management-solutions) 安装管理解决方案时，系统会提示他们选择 [OMS 工作区和自动管理帐户](operations-management-suite-solutions.md#oms-workspace-and-automation-account)。  这些用于填充每个标准参数的值。  系统不提示用户直接提供标准参数的值，但会提示他们提供任何其他参数的值。

用户安装解决方案[另一种方法](operations-management-suite-solutions.md#finding-and-installing-management-solutions)时，必须为所有标准参数和所有其他参数提供一个值。

下面显示了一个示例参数。  

    "startTime": {
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
下表列出了所有管理解决方案的标准参数。  从 Azure Marketplace 或快速入门模板安装解决方案时，这些值用于为用户进行填充而不是提示他们。  如果使用其他方法安装解决方案，则用户必须为其提供值。

> [!NOTE]
> Azure Marketplace 和快速入门模板中的用户界面正在等待表中的参数名称。  如果使用不同的参数名称，则将对用户进行提示，这些参数不会自动填充。
>
>

| 参数 | 类型 | 说明 |
|:--- |:--- |:--- |
| accountName |字符串 |Azure 自动化帐户名称。 |
| pricingTier |字符串 |Log Analytics 工作区和 Azure 自动化帐户的定价层。 |
| regionId |字符串 |Azure 自动化帐户的区域。 |
| solutionName |字符串 |解决方案名称。  如果要通过快速入门模板部署解决方案，则应将 solutionName 定义为参数，以便能够定义字符串，而无需用户指定一个字符串。 |
| workspaceName |字符串 |Log Analytics 工作区名称。 |
| workspaceRegionId |字符串 |Log Analytics 工作区的区域。 |


以下是可以复制并粘贴到解决方案文件的标准参数的结构。  

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
        }
    }


请参阅解决方案的其他元素中语法为 **parameters('parameter name')** 的参数值。  例如，若要访问工作区名称，将使用 **parameters('workspaceName')**

## <a name="variables"></a>变量
[Variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) 是将在管理解决方案的其余部分使用的值。  这些值不公开给安装解决方案的用户。  它们旨在向作者提供一个位置，供作者在该位置中管理可在整个解决方案中使用多次的值。 应将任何特定于解决方案的值放在变量中，而不是在 **resources** 元素中对其进行硬编码。  这使代码更具可读性，并允许在更高版本中轻松地更改这些值。

下面是具有在解决方案中使用的典型参数的 **variables** 元素示例。

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

引用整个解决方案中的变量值时的语法为 **variables('variable name')** 的变量值。  例如，若要访问 SolutionName 变量，需使用 **variables('SolutionName')**。

还可定义包含多组值的复杂变量。  这些复杂变量对以下管理解决方案特别有用：为不同类型的资源定义多个属性。  例如，可将如上所示的解决方案变量重构到以下内容。

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

在此情况下，引用整个解决方案中语法为 **variables('variable name').property** 的变量值。  例如，若要访问 Solution Name 变量，需使用 **variables('Solution').Name**。

## <a name="resources"></a>资源
[Resources](../azure-resource-manager/resource-group-authoring-templates.md#resources) 用于定义管理解决方案要安装和配置的不同资源。  这将是模板的最大且最复杂的部分。  可在[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md#resources)中获取资源元素的结构和完整说明。  本文档的其他文章中详述了通常需定义的其他资源。 


### <a name="dependencies"></a>依赖项
**dependsOn** 元素指定对另一个资源的[依赖](../azure-resource-manager/resource-group-define-dependencies.md)。  安装解决方案时，资源的所有依赖均已创建后才能创建资源。  例如，如果解决方案使用[作业资源](operations-management-suite-solutions-resources-automation.md#automation-jobs)安装，则该解决方案需要[启动 runbook](operations-management-suite-solutions-resources-automation.md#runbooks)。  作业资源将依赖于 runbook 资源，以确保在创建作业之前创建 runbook。

### <a name="oms-workspace-and-automation-account"></a>OMS 工作区和自动化帐户
管理解决方案需要 [OMS 工作区](../log-analytics/log-analytics-manage-access.md)来包含视图，也需要[自动化帐户](../automation/automation-security-overview.md#automation-account-overview)来包含 runbook 和相关资源。  这些内容在解决方案中的资源创建之前必须已经存在，并且不能在解决方案本身中定义。  部署解决方案时，用户将[指定工作区和帐户](operations-management-suite-solutions.md#oms-workspace-and-automation-account)，但作为作者，你应考虑以下几点。

## <a name="solution-resource"></a>解决方案资源
每个解决方案在 **resources** 元素中都需要资源项，用于定义解决方案本身。  该资源项的类型为 **Microsoft.OperationsManagement/solutions**，并且具有以下结构。 这包括常用于定义解决方案属性的[标准参数](#parameters)和[变量](#variables)。


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspacename'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>依赖项
解决方案资源必须具有针对解决方案中所有其他资源的[依赖](../azure-resource-manager/resource-group-define-dependencies.md)，因为它们在解决方案创建之前必须已经存在。  通过在 **dependsOn** 元素中添加每个资源的项实现此目的。

### <a name="properties"></a>属性
解决方案资源具有下表中的属性。  这包括由用于定义安装解决方案后如何管理资源的解决方案引用和包含的资源。  解决方案中的每个资源应在 **referencedResources** 或 **containedResources** 属性中列出。

| 属性 | 说明 |
|:--- |:--- |
| workspaceResourceId |*<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>* 窗体中 Log Analytics 工作区的 ID。 |
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



## <a name="sample"></a>示例
可在以下位置查看具有解决方案资源的解决方案文件示例。

- [自动化资源](operations-management-suite-solutions-resources-automation.md#sample)
- [搜索和警报资源](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>后续步骤
* [已保存的搜索和警报](operations-management-suite-solutions-resources-searches-alerts.md)到管理解决方案。
* [将视图](operations-management-suite-solutions-resources-views.md)添加到管理解决方案。
* [将 runbook 和其他自动化资源添加](operations-management-suite-solutions-resources-automation.md)到管理解决方案。
* 了解[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)的详细信息。
* 在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates)中搜索不同 Resource Manager 模板的示例。

