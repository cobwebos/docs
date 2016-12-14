---
title: "OMS 解决方案中的自动化资源 | Microsoft 文档"
description: "OMS 中的解决方案通常在 Azure 自动化中包含 Runbook 以自动执行各种进程，例如收集和处理监控数据。  本文介绍如何在解决方案中包含 Runbook 和其相关资源。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e115fd3b5f29dbcbfd9f85ebb215d950539cc1e3


---
# <a name="automation-resources-in-oms-solutions-preview"></a>OMS 解决方案中的自动化资源（预览版）
> [!NOTE]
> 这是在 OMS 中创建管理解决方案的初步文档，当前仅提供预览版。 如下所述的全部架构均会有变动。   
> 
> 

[OMS 中的管理解决方案](operations-management-suite-solutions.md)通常在 Azure 自动化中包含 Runbook 以自动执行各种进程，例如收集和处理监控数据。  除了 Runbook，自动化帐户还包含资产，例如支持在解决方案中使用的 Runbook 的变量和计划。  本文介绍如何在解决方案中包含 Runbook 和其相关资源。

> [!NOTE]
> 本文中的示例使用管理解决方案需要或通用的参数和变量，[在 Operations Management Suite (OMS) 中创建管理解决方案](operations-management-suite-solutions-creating.md)对此进行了介绍 
> 
> 

## <a name="prerequisites"></a>先决条件
本文假设你已经熟悉如何[创建管理解决方案](operations-management-suite-solutions-creating.md)及解决方案文件的结构。

## <a name="samples"></a>示例
可以从 [GitHub 的快速入门模板](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates)中获取自动化资源的示例资源管理器模板。

## <a name="automation-account"></a>自动化帐户
Azure 自动化中的所有资源都包含在[自动化帐户](../automation/automation-security-overview.md#automation-account-overview)中。  如 [OMS 工作区和自动化帐户](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)中所述，自动化帐户不包括在管理解决方案中，但必须存在才可以安装解决方案。  如果没有，解决方案安装将失败。

每个自动化资源的名称都包含其自动化帐户名称。  这是在具有 **accountName** 参数的解决方案中完成的，如以下 Runbook 资源示例所示。

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
[Azure 自动化 Runbook](../automation/automation-runbook-types.md) 资源具有类型 **Microsoft.Automation/automationAccounts/runbooks** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| runbookType |指定 Runbook 的类型。 <br><br> 脚本 - PowerShell 脚本 <br>PowerShell - PowerShell 工作流 <br> GraphPowerShell - 图形 PowerShell 脚本 Runbook <br> GraphPowerShellWorkflow - 图形 PowerShell 工作流 Runbook |
| logProgress |指定是否应为 Runbook 生成[进度记录](../automation/automation-runbook-output-and-messages.md)。 |
| logVerbose |指定是否应为 Runbook 生成[详细记录](../automation/automation-runbook-output-and-messages.md)。 |
| description |Runbook 的可选说明。 |
| publishContentLink |指定 Runbook 的内容。 <br><br>uri - Runbook 内容的 URI。  这将是 PowerShell 和脚本 Runbook 的 .ps1 文件，以及为图形 Runbook 导出的图形 Runbook 文件。  <br> version - 你自己跟踪的 Runbook 版本。 |

下面是 Runbook 资源的示例。  在这种情况下，它将从 [PowerShell 库](https://www.powershellgallery.com)检索 Runbook。

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>启动 Runbook
可以通过两种方法在管理解决方案中启动 Runbook。

* 若要在安装解决方案时启动 Runbook，如下所述创建[作业资源](#automation-jobs)。
* 若要按计划启动 Runbook，如下所述创建[计划资源](#schedules)。 

## <a name="automation-jobs"></a>自动化作业
若要在安装管理解决方案时启动 Runbook，应创建**作业**资源。  作业资源具有类型 **Microsoft.Automation/automationAccounts/jobs** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Runbook |包含 Runbook 名称的单个 **name** 实体。 |
| 参数 |Runbook 所需的每个参数的实体。 |

作业包括 Runbook 名称和发送到 Runbook 的任何参数值。  作业必须[依赖于](operations-management-suite-solutions-creating.md#resources)自必须在作业之前创建 Runbook 以来启动的 Runbook。  还可以为应在当前 Runbook 之前完成的 Runbook 创建对其他作业的依赖关系。

作业资源的名称必须包含一个通常由参数分配的 GUID。  有关 GUID 参数的详细信息，可参阅[在 Operations Management Suite (OMS) 中创建解决方案](operations-management-suite-solutions-creating.md#parameters)。  

下面是在安装管理解决方案时启动 Runbook 的作业资源示例。  在该 Runbook 启动之前必须完成另一个，因此它在该 Runbook 的作业中具有依赖关系。  作业的详细信息是通过参数和变量提供的，而不是直接指定。

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>证书
[Azure 自动化证书](../automation/automation-certificates.md)具有类型 **Microsoft.Automation/automationAccounts/certificates** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| base64Value |证书的 Base 64 值。 |
| thumbprint |证书的指纹。 |

下面是证书资源的示例。

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>凭据
[Azure 自动化凭据](../automation/automation-credentials.md)具有类型 **Microsoft.Automation/automationAccounts/credentials** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| userName |凭据的用户名。 |
| password |凭据的密码。 |

下面是证书资源的示例。

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>计划
[Azure 自动化计划](../automation/automation-schedules.md)具有类型 **Microsoft.Automation/automationAccounts/schedules** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| description |计划的可选说明。 |
| startTime |指定计划的开始时间作为 DateTime 对象。 如果它可以转换为有效的 DateTime，则可以提供一个字符串。 |
| isEnabled |指定是否启用计划。 |
| interval |计划的间隔类型。<br><br>day<br>hour |
| frequency |计划应在数天或数小时内触发的频率。 |

下面是计划资源的示例。

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>变量
[Azure 自动化变量](../automation/automation-variables.md)具有类型 **Microsoft.Automation/automationAccounts/variables** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| description |变量的可选说明。 |
| isEncrypted |指定是否应加密变量。 |
| type |变量的数据类型。 |
| value |变量的值。 |

下面是变量资源的示例。

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>模块
管理解决方案不需要定义Runbook 使用的[全局模块](../automation/automation-integration-modules.md)，因为它们始终都将可用。  需要包括 Runbook 使用的任何其他模块的资源，并且 Runbook 应依赖于模块资源以确保它在 Runbook 之前创建。

[集成模块](../automation/automation-integration-modules.md)具有类型 **Microsoft.Automation/automationAccounts/modules** 和下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| contentLink |指定模块的内容。 <br><br>uri - Runbook 内容的 URI。  这将是 PowerShell 和脚本 Runbook 的 .ps1 文件，以及为图形 Runbook 导出的图形 Runbook 文件。  <br> version - 你自己跟踪的 Runbook 版本。 |

下面是模块资源的示例。

    {        
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>更新模块
如果更新的管理解决方案包含使用计划的 Runbook，并且新版本的解决方案具有该 Runbook 使用的新模块，则该 Runbook 可以使用旧版本的模块。  应在解决方案中包括以下 Runbook，并创建一个作业，以在任何其他 Runbook 之前运行它们。  这将确保在加载 Runbook 之前视需要更新任何模块。

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) 将确保解决方案中的 Runbook 使用的所有模块都是最新版本。  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) 将重新注册所有的计划资源来确保 Runbook 链接到使用最新模块的 Runbook。

以下是为支持模块更新的解决方案所需的元素示例。

    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>后续步骤
* [将视图添加到解决方案](operations-management-suite-solutions-resources-views.md)以可视化收集的数据。




<!--HONumber=Nov16_HO3-->


