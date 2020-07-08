---
title: 使用 Azure 资源管理器模板创建自动化帐户 | Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器模板来创建 Azure 自动化帐户。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/09/2020
ms.openlocfilehash: 6b26db522db246add48941da9af4784ed2942a0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84661028"
---
# <a name="create-an-automation-account-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建自动化帐户

可以使用 [Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)在资源组中创建 Azure 自动化帐户。 本文提供了一个示例模板，可执行以下操作：

* 自动创建 Azure Monitor Log Analytics 工作区。
* 自动创建 Azure 自动化帐户。
* 将自动化帐户链接到 Log Analytics 工作区。

该模板不会自动启用 Azure 或非 Azure 虚拟机。 

>[!NOTE]
>使用 Azure 资源管理器模板时，不支持创建自动化运行方式帐户。 若要从门户或使用 PowerShell 手动创建运行方式帐户，请参阅[管理运行方式帐户](manage-runas-account.md)。

完成这些步骤后，你需要为自动化帐户[配置诊断设置](automation-manage-send-joblogs-log-analytics.md)，以将 runbook 作业状态和作业流发送到链接的 Log Analytics 工作区。 

## <a name="api-versions"></a>API 版本

下表列出了此示例中使用的资源的 API 版本。

| 资源 | 资源类型 | API 版本 |
|:---|:---|:---|
| 工作区 | workspaces | 2020-03-01-预览 |
| 自动化帐户 | automation | 2018-06-30 | 

## <a name="before-you-use-the-template"></a>使用模版之前

如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell Az 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。 使用 PowerShell 时，部署使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果选择在本地安装并使用 Azure CLI，则本文要求运行 2.1.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI 时，此部署使用 [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

已配置 JSON 模板，以提示你输入：

* 工作区的名称。
* 要在其中创建工作区的区域。
* 启用资源或工作区权限。
* 自动化帐户的名称。
* 要在中创建自动化帐户的区域。

模板中的以下参数设置为 Log Analytics 工作区的默认值：

* sku 默认为“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布。
* dataRetention 默认为 30 天。

>[!WARNING]
>如果要在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的定价模型，则唯一有效的 Log Analytics 定价层为 PerGB2018。
>

JSON 模板为其他参数指定默认值，这些参数将会用作环境中的标准配置。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

如果你不熟悉 Azure 自动化和 Azure Monitor，请务必了解以下配置详细信息。 当你尝试创建、配置和使用链接到新自动化帐户的 Log Analytics 工作区时，这些信息有助于避免出错。

* 查看[其他详细信息](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)以充分了解工作区配置选项，如访问控制模式、定价层、保留期和产能预留级别。

* 查看[工作区映射](how-to/region-mappings.md)，以内联方式或在参数文件中指定支持的区域。 只有某些区域支持链接订阅中的 Log Analytics 工作区和自动化帐户。

* 如果刚开始使用 Azure Monitor 日志，并且尚未部署工作区，则应查看[工作区设计指南](../azure-monitor/platform/design-logs-deployment.md)。 它将有助于了解访问控制以及建议组织采用的设计实现策略。

## <a name="deploy-the-template"></a>部署模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days to retain data."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
              }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
            }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. 按要求编辑模板。 请考虑创建[资源管理器参数文件](../azure-resource-manager/templates/parameter-files.md)，而不是将参数作为内联值传递。

3. 将此文件以“deployAzAutomationAccttemplate.json”文件名保存到本地文件夹。

4. 已做好部署此模板的准备。 可以使用 PowerShell 或 Azure CLI。 当系统提示输入工作区和自动化帐户名称时，请提供一个在所有 Azure 订阅中全局唯一的名称。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示。

    ![部署完成后的示例结果](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>后续步骤

若要将 runbook 作业状态和作业流转发到链接的 Log Analytics 工作区，请查看[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。 这会使用 Azure PowerShell 命令配置自动化帐户的诊断设置，从而完成用于将日志发送到工作区以进行分析的集成。 
