---
title: 使用 Azure 资源管理器模板加入更新管理 |Microsoft Docs
description: 可以使用 Azure 资源管理器模板来加入 Azure 自动化更新管理解决方案。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925795"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板内置更新管理解决方案

可以使用[azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)在资源组中启用 azure Automation 更新管理解决方案。 本文提供自动执行以下操作的示例模板：

* 创建 Azure Monitor Log Analytics 工作区。
* 创建 Azure 自动化帐户。
* 将自动化帐户链接到 Log Analytics 工作区（如果尚未链接）。
* 载入 Azure 自动化更新管理解决方案

该模板不会自动载入一个或多个 Azure 或非 Azure Vm。

如果你已在订阅的受支持区域中部署了 Log Analytics 工作区和自动化帐户，则这些帐户不会链接在一起，并且工作区尚未部署更新管理解决方案，使用此模板可成功创建链接并部署更新管理解决方案。 

## <a name="api-versions"></a>API 版本

下表列出了此示例中使用的资源的 API 版本。

| 资源 | 资源类型 | API 版本 |
|:---|:---|:---|
| 工作区 | workspaces | 2017-03-15-preview |
| 自动化帐户 | automation | 2015-10-31 | 
| 解决方案 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>使用模板之前

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell Az 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。 使用 Azure PowerShell，部署将使用[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 版本2.1.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI，此部署使用[az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

JSON 模板配置为提示你：

* 工作区的名称
* 要在其中创建工作区的区域
* 自动化帐户的名称
* 要在其中创建帐户的区域

JSON 模板为可能在您的环境中用作标准配置的其他参数指定默认值。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

使用 "Log Analytics" 工作区的默认值设置模板中的以下参数：

* sku - 默认为新的“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布
* 数据保持期-默认为三十天

>[!WARNING]
>如果在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的新定价模型，则唯一有效的 Log Analytics 定价层为 **PerGB2018**。
>

>[!NOTE]
>使用此模板之前，请查看[其他详细信息](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)，以充分了解工作区配置选项，如访问控制模式、定价层、保留期和容量保留级别。 如果你不熟悉 Azure Monitor 日志，并且尚未部署工作区，则应查看[工作区设计](../azure-monitor/platform/design-logs-deployment.md)指南以了解访问控制，并了解我们建议为你的组织提供的设计实现策略。

## <a name="deploy-template"></a>部署模板

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
        "pricingTier": {
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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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
                "description": "Specify the location in which to create the Automation account."
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
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
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
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
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
    ```

2. 按要求编辑模板。

3. 将此文件作为 deployUMSolutiontemplate 保存到本地文件夹。

4. 已做好部署此模板的准备。 可以使用 PowerShell 或 Azure CLI。 当系统提示输入工作区和自动化帐户名称时，请提供在所有 Azure 订阅中全局唯一的名称。

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

    ![部署完成后的示例结果](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>后续步骤

部署了更新管理解决方案后，你可以启用用于管理的 Vm，查看更新评估，并部署更新以使其符合要求。

- 用于一个或多个 Azure 计算机的[Azure 自动化帐户](automation-onboard-solutions-from-automation-account.md)，并为非 azure 计算机手动。

- 对于 "Azure 门户中的" 虚拟机 "页上的单个 Azure VM。 此方案适用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。

- 通过在 Azure 门户的 "**虚拟机**" 页中选择[多个 Azure vm](manage-update-multi.md) 。 