---
title: 使用 Azure 资源管理器模板启用更新管理 | Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器模板启用更新管理。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: 148ed0eab3e74d96ea6dbf1f507cd0ea53ad2fdc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073778"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用“更新管理”

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)在资源组中启用 Azure 自动化更新管理功能。 本文提供了可自动执行以下操作的示例模板：

* 自动创建 Azure Monitor Log Analytics 工作区。
* 自动创建 Azure 自动化帐户。
* 将自动化帐户链接到 Log Analytics 工作区。
* 将示例自动化 runbook 添加到帐户。
* 启用更新管理功能。

该模板不会自动对一个或多个 Azure VM 或非 Azure VM 启用更新管理。

如果已在订阅支持的区域中部署了 Log Analytics 工作区和自动化帐户，不会链接该工作区和帐户。 使用此模板可以成功创建链接并部署更新管理。

>[!NOTE]
>使用 ARM 模板时，不支持创建自动化运行方式帐户。 若要从门户或使用 PowerShell 手动创建运行方式帐户，请参阅[管理运行方式帐户](../manage-runas-account.md)。

完成这些步骤后，你需要为自动化帐户[配置诊断设置](../automation-manage-send-joblogs-log-analytics.md)，以将 runbook 作业状态和作业流发送到链接的 Log Analytics 工作区。

## <a name="api-versions"></a>API 版本

下表列出了此示例中使用的资源的 API 版本。

| 资源 | 资源类型 | API 版本 |
|:---|:---|:---|
| [工作区](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [自动化帐户](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [工作区关联的服务](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [解决方案](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>使用模板之前

已配置 JSON 模板，以提示你输入：

* 工作区的名称。
* 要在其中创建工作区的区域。
* 自动化帐户的名称。
* 要在其中创建自动化帐户的区域。

模板中的以下参数设置为 Log Analytics 工作区的默认值：

* sku 默认为“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布。
* dataRetention 默认为 30 天。

>[!WARNING]
>如果要在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的定价模型，则唯一有效的 Log Analytics 定价层为 PerGB2018。
>

JSON 模板为其他参数指定默认值，这些参数将会用作环境中的标准配置。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的详细信息，请参阅[使用 ARM 模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)。

如果你不熟悉 Azure 自动化和 Azure Monitor，请务必了解以下配置详细信息。 当你尝试创建、配置和使用链接到新自动化帐户的 Log Analytics 工作区时，这些信息有助于避免出错。

* 查看[其他详细信息](../../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)以充分了解工作区配置选项，如访问控制模式、定价层、保留期和产能预留级别。

* 查看[工作区映射](../how-to/region-mappings.md)，以内联方式或在参数文件中指定支持的区域。 只有某些区域支持链接订阅中的 Log Analytics 工作区和自动化帐户。

* 如果刚开始使用 Azure Monitor 日志，并且尚未部署工作区，则应查看[工作区设计指南](../../azure-monitor/platform/design-logs-deployment.md)。 它将有助于了解访问控制以及建议组织采用的设计实现策略。

## <a name="deploy-template"></a>部署模板

1. 将以下 JSON 语法复制并粘贴到该文件中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
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
                    "description": "Specifies the location in which to create the Automation account."
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
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
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
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. 按要求编辑模板。 请考虑创建[资源管理器参数文件](../../azure-resource-manager/templates/parameter-files.md)，而不是将参数作为内联值传递。

3. 将此文件以“deployUMSolutiontemplate.json”文件名保存到本地文件夹。

4. 已做好部署此模板的准备。 可以使用 PowerShell 或 Azure CLI。 当系统提示输入工作区和自动化帐户名称时，提供一个在所有 Azure 订阅中全局唯一的名称。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

    ![部署完成后的示例结果](media/update-mgmt-enable-template/template-output.png)

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，打开创建的自动化帐户。

3. 在左侧窗格中，选择“Runbook”。 “Runbook”页上列出了三个使用自动化帐户创建的教程 runbook。

    ![使用自动化帐户创建的教程 runbook](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 在左侧窗格中，选择“关联的工作区”。 “关联的工作区”页上显示了先前指定的、已关联到自动化帐户的 Log Analytics 工作区。

    ![已关联到 Log Analytics 工作区的自动化帐户](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. 在左侧窗格中，选择 " **更新管理**"。 在 " **更新管理** " 页上，它会显示 "评估" 页，其中没有任何信息作为仅启用的结果，并且没有为管理配置计算机。

    ![更新管理功能评估视图](./media/update-mgmt-enable-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>清理资源

当你不再需要它们时，请删除 "Log Analytics" 工作区中的 **更新** 解决方案，将自动化帐户从工作区中取消链接，然后删除自动化帐户和工作区。

## <a name="next-steps"></a>后续步骤

* 若要对 VM 使用更新管理，请参阅[管理 VM 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。

* 如果不再想使用更新管理并且希望删除它，请参阅[删除更新管理功能](update-mgmt-remove-feature.md)中的说明。

* 若要从更新管理中删除 VM，请参阅[从更新管理中删除 VM](update-mgmt-remove-vms.md)。