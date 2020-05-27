---
title: 管理解决方案中的 Azure 自动化资源 | Microsoft Docs
description: 管理解决方案通常在 Azure 自动化中包含 Runbook 以自动执行各种进程，例如收集和处理监控数据。  本文介绍如何在解决方案中包含 Runbook 和其相关资源。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3b1b134afbc4a13d7888281a82609d444cee377
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682867"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>将 Azure 自动化资源添加到管理解决方案（预览版）
> [!NOTE]
> 这是用于创建当前处于预览版的管理解决方案的初步文档。 如下所述的全部架构均会有变动。   


[管理解决方案]( solutions.md)通常在 Azure 自动化中包含 Runbook 以自动执行各种进程，例如收集和处理监控数据。  除了 Runbook，自动化帐户还包含资产，例如支持在解决方案中使用的 Runbook 的变量和计划。  本文介绍如何在解决方案中包含 Runbook 和其相关资源。

> [!NOTE]
> 本文中的示例使用管理解决方案需要或通用的参数和变量，[在 Azure 中设计和开发解决方案]( solutions-creating.md)中对它们进行了介绍 


## <a name="prerequisites"></a>先决条件
本文假设已熟悉以下信息。

- 如何[创建管理解决方案]( solutions-creating.md)。
- [解决方案文件]( solutions-solution-file.md)的结构。
- 如何[创作 Resource Manager 模板](../../azure-resource-manager/templates/template-syntax.md)

## <a name="automation-account"></a>自动化帐户
Azure 自动化中的所有资源都包含在[自动化帐户](../../automation/automation-security-overview.md)中。  如 [Log Analytics 工作区和自动化帐户]( solutions.md#log-analytics-workspace-and-automation-account)中所述，自动化帐户不包括在管理解决方案中，但必须存在才可以安装解决方案。  如果没有，解决方案安装会失败。

每个自动化资源的名称都包含其自动化帐户的名称。  这是在具有 **accountName** 参数的解决方案中完成的，如以下 Runbook 资源示例所示。

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
应在解决方案文件中包含解决方案所使用的任何 runbook，以便在安装解决方案时创建这些 runbook。  但是，不能在模板中包含 runbook 的正文，因此应将 runbook 发布到任何安装解决方案的用户均可访问的公共位置。

[Azure 自动化 runbook](../../automation/automation-runbook-types.md) 资源具有类型 **Microsoft.Automation/automationAccounts/runbooks** 和以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


下表介绍了 runbook 的属性。

| properties | 说明 |
|:--- |:--- |
| runbookType |指定 Runbook 的类型。 <br><br> 脚本 - PowerShell 脚本 <br>PowerShell - PowerShell 工作流 <br> GraphPowerShell - 图形 PowerShell 脚本 Runbook <br> GraphPowerShellWorkflow - 图形 PowerShell 工作流 Runbook |
| logProgress |指定是否应为 Runbook 生成[进度记录](../../automation/automation-runbook-output-and-messages.md)。 |
| logVerbose |指定是否应为 Runbook 生成[详细记录](../../automation/automation-runbook-output-and-messages.md)。 |
| description |Runbook 的可选说明。 |
| publishContentLink |指定 Runbook 的内容。 <br><br>uri - Runbook 内容的 URI。  这会是 PowerShell 和脚本 Runbook 的 .ps1 文件，以及为图形 Runbook 导出的图形 Runbook 文件。  <br> version - 自己跟踪的 Runbook 版本。 |


## <a name="automation-jobs"></a>自动化作业
在 Azure 自动化中启动 runbook 时，会创建一个自动化作业。  可以会自动化作业资源添加到解决方案，以便在安装管理解决方案时自动启动 runbook。  此方法通常用于启动用于对解决方案进行初始配置的 runbook。  若要定期启动 runbook，请创建[计划](#schedules)和[作业计划](#job-schedules)

作业资源具有类型 **Microsoft.Automation/automationAccounts/jobs** 和以下结构。  这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

下表介绍了自动化作业的属性。

| properties | 说明 |
|:--- |:--- |
| Runbook |包含要启动的 Runbook 名称的单个 name 实体。 |
| parameters |Runbook 所需的每个参数值的实体。 |

作业包括 Runbook 名称和发送到 Runbook 的任何参数值。  作业应[依赖于]( solutions-solution-file.md#resources)自必须在作业之前创建 Runbook 以来启动的 Runbook。  如果有多个应启动的 runbook，则可以通过让一个作业依赖于任何其他应先运行的作业来定义其顺序。

作业资源的名称必须包含一个通常由参数分配的 GUID。  有关 GUID 参数的详细信息，请参阅[在 Azure 中创建管理解决方案文件]( solutions-solution-file.md#parameters)。  


## <a name="certificates"></a>证书
[Azure 自动化证书](../../automation/automation-certificates.md)具有类型 **Microsoft.Automation/automationAccounts/certificates** 和以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



下表介绍了证书资源的属性。

| properties | 说明 |
|:--- |:--- |
| base64Value |证书的 Base 64 值。 |
| thumbprint |证书的指纹。 |



## <a name="credentials"></a>凭据
[Azure 自动化凭据](../../automation/automation-credentials.md)具有类型 **Microsoft.Automation/automationAccounts/credentials** 和以下结构。  这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

下表介绍了凭据资源的属性。

| properties | 说明 |
|:--- |:--- |
| userName |凭据的用户名。 |
| password |凭据的密码。 |


## <a name="schedules"></a>计划
[Azure 自动化计划](../../automation/automation-schedules.md)具有类型 **Microsoft.Automation/automationAccounts/schedules** 和以下结构。 这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

下表介绍了计划资源的属性。

| properties | 说明 |
|:--- |:--- |
| description |计划的可选说明。 |
| startTime |指定计划的开始时间作为 DateTime 对象。 如果它可以转换为有效的 DateTime，则可以提供一个字符串。 |
| isEnabled |指定是否启用计划。 |
| interval |计划的间隔类型。<br><br>day<br>hour |
| 频率 |计划应在数天或数小时内触发的频率。 |

计划的开始时间值必须晚于当前时间。  不能通过变量来提供此值，因为无法知道何时要安装。

在解决方案中使用计划资源时，请使用以下两个策略之一。

- 对计划的开始时间使用参数。  这会在安装解决方案时提示用户提供一个值。  如果有多个计划，可以对多个计划使用单个参数值。
- 可使用安装解决方案时启动的 runbook 创建计划。  这样用户将无需指定时间，但你不能在解决方案中包含计划，以免在删除解决方案时删除计划。


### <a name="job-schedules"></a>作业计划
作业计划资源将 runbook 与一个计划链接。  作业计划资源具有 Microsoft.Automation/automationAccounts/jobSchedules 类型和以下结构。  这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


下表介绍了作业计划的属性。

| properties | 说明 |
|:--- |:--- |
| 计划名称 |包含计划名称的单个 **name** 实体。 |
| runbook 名称  |包含 Runbook 名称的单个 **name** 实体。  |



## <a name="variables"></a>变量
[Azure 自动化变量](../../automation/automation-variables.md)具有类型 **Microsoft.Automation/automationAccounts/variables** 和以下结构。  这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

下表介绍了变量资源的属性。

| properties | 说明 |
|:--- |:--- |
| description | 变量的可选说明。 |
| isEncrypted | 指定是否应加密变量。 |
| type | 当前此属性无效。  初始值决定该变量的数据类型。 |
| 值 | 变量的值。 |

> [!NOTE]
> 当前，“类型”属性对正在创建的变量无效。  变量的数据类型由值决定。  

如果设置变量的初始值，则该值必须配置为正确的数据类型。  下表提供了允许的不同数据类型及其语法。  请注意，JSON 中的值应始终使用引号括起来，任何特殊字符位于引号内。  例如，字符串值应通过将字符串用引号括起来进行指定（使用转义字符 (\\)），而数字值应使用一组引号进行指定。

| 数据类型 | 说明 | 示例 | 解析为 |
|:--|:--|:--|:--|
| 字符串   | 将值括在双引号中。  | “\"Hello world\"” | “Hello world” |
| numeric  | 用单引号将数字值括起来。| “64” | 64 |
| boolean  | 引号中的“true”或“false”。  请注意，此值必须为小写。 | "true" | true |
| datetime | 序列化日期值。<br>可以在 PowerShell 中使用 ConvertTo-Json cmdlet 为特定日期生成此值。<br>示例：get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>模块
管理解决方案不需要定义 Runbook 使用的[全局模块](../../automation/automation-integration-modules.md)，因为它们始终都在自动化帐户中可用。  需要包括 runbook 使用的任何其他模块资源。

[集成模块](../../automation/automation-integration-modules.md)具有类型 **Microsoft.Automation/automationAccounts/modules** 和以下结构。  这包括常见变量和参数，以便可以将此代码片段复制并粘贴到解决方案文件，并更改参数名称。

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


下表介绍了模块资源的属性。

| properties | 说明 |
|:--- |:--- |
| contentLink |指定模块的内容。 <br><br>uri - 模块内容的 URI。  这会是 PowerShell 和脚本 Runbook 的 .ps1 文件，以及为图形 Runbook 导出的图形 Runbook 文件。  <br> version - 自己跟踪的模块版本。 |

runbook 应依赖于模块资源，以确保模块资源在 runbook 之前创建。

### <a name="updating-modules"></a>更新模块
如果更新的管理解决方案包含使用计划的 Runbook，并且新版本的解决方案具有该 Runbook 使用的新模块，则该 Runbook 可以使用旧版本的模块。  应在解决方案中包括以下 Runbook，并创建一个作业，以在任何其他 Runbook 之前运行它们。  这会确保在加载 Runbook 之前视需要更新任何模块。

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) 将确保解决方案中的 Runbook 使用的所有模块都是最新版本。  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) 将重新注册所有的计划资源来确保 Runbook 链接到使用最新模块的 Runbook。




## <a name="sample"></a>示例
以下是包含下列资源的解决方案示例：

- Runbook。  这是公共 GitHub 存储库中存储的示例 runbook。
- 安装解决方案时启动 runbook 的自动化作业。
- 用于定期启动 runbook 的计划和作业计划。
- 证书。
- 凭据。
- 变量。
- 模块。  这是用于将数据写入到 Log Analytics 的 [OMSIngestionAPI 模块](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)。 

此示例使用的是解决方案中常用的[标准解决方案参数]( solutions-solution-file.md#parameters)变量，不同于资源定义中使用的硬编码值。


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>后续步骤
* [将视图添加到解决方案]( solutions-resources-views.md)以可视化收集的数据。
