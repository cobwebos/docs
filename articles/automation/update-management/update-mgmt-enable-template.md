---
title: 使用 Azure 资源管理器模板启用更新管理 | Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器模板启用更新管理。
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: 37f4b7208d7c8fafcd3aa1d25106a2cd5e4949c6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449938"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用“更新管理”

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)在资源组中启用 Azure 自动化更新管理功能。 本文提供了可自动执行以下操作的示例模板：

* 创建 Azure Monitor Log Analytics 工作区。
* 创建 Azure 自动化帐户。
* 将自动化帐户链接到 Log Analytics 工作区（如果尚未链接）。
* 启用更新管理。

该模板不会自动启用一个或多个 Azure 或非 Azure Vm 上的更新管理。

如果已在订阅中支持的区域中部署了 Log Analytics 工作区和自动化帐户，则不会链接该工作区和帐户。 使用此模板将成功创建链接并部署更新管理。

## <a name="api-versions"></a>API 版本

下表列出了此模板中使用的资源的 API 版本。

| 资源 | 资源类型 | API 版本 |
|:---|:---|:---|
| 工作区 | workspaces | 2020-03-01-预览 |
| 自动化帐户 | automation | 2018-06-30 |
| 解决方案 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>使用模板之前

如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell Az 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) 以创建与 Azure 的连接。 使用 Azure PowerShell 时，部署使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.1.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 使用 Azure CLI 时，此部署使用 [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)。 

已配置 JSON 模板，以提示你输入：

* 工作区的名称。
* 要在其中创建工作区的区域。
* 启用资源或工作区权限。
* 自动化帐户的名称。
* 要在其中创建帐户的区域。

JSON 模板可为其他参数指定默认值，这些参数可能用作环境中的标准配置。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的更多信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)。

模板中的以下参数设置为 Log Analytics 工作区的默认值：

* sku - 默认为新的“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布
* 数据保留 - 默认为 30 天

>[!WARNING]
>如果在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的新定价模型，则唯一有效的 Log Analytics 定价层为 **PerGB2018**。
>

JSON 模板可为其他参数指定默认值，这些参数可能用作环境中的标准配置。 可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的更多信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)。

如果你不熟悉 Azure 自动化和 Azure Monitor，则务必要了解以下配置详细信息，以免在尝试创建、配置和使用链接到新自动化帐户的 Log Analytics 工作区时出现错误，这一点非常重要。

* 查看[其他详细信息](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)以充分了解工作区配置选项，如访问控制模式、定价层、保留期和产能预留级别。

* 由于只有某些区域支持链接订阅中的 Log Analytics 工作区和自动化帐户，因此请查看[工作区映射](../how-to/region-mappings.md)以指定内联或参数文件中支持的区域。

* 如果你不熟悉 Azure Monitor 日志，并且尚未部署工作区，则应查看[工作区设计](../../azure-monitor/platform/design-logs-deployment.md)指南以了解访问控制，并了解我们为组织所推荐的设计实现策略。

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
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
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

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 Vm，请参阅[管理 vm 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。

* 如果不再想要使用更新管理并且想要删除它，请参阅[删除更新管理功能](update-mgmt-remove-feature.md)中的说明。

* 若要从更新管理中删除 VM，请参阅[从更新管理中删除 VM](update-mgmt-remove-vms.md)。
