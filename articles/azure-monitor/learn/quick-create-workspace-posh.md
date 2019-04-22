---
title: 使用 Azure PowerShell 创建 Log Analytics 工作区 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: 6f27aeb65cb9077011e662c165ca26202546db26
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905726"
---
# <a name="create-a-log-analytics-workspace-with-azure-powershell"></a>使用 Azure PowerShell 创建 Log Analytics 工作区

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure PowerShell 模块在 Azure Monitor 中部署 Log Analytics 工作区。 Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。 如果打算从以下源收集数据，则需要 Log Analytics 工作区：

* 订阅中的 Azure 资源  
* 受 System Center Operations Manager 监视的本地计算机  
* System Center Configuration Manager 中的设备集合  
* Azure 存储中的诊断或日志数据  
 
对于其他源，如环境中的 Azure VM 和 Windows VM 或 Linux VM，请参阅以下主题：

* [从 Azure 虚拟机收集数据](../learn/quick-collect-azurevm.md)
* [从混合 Linux 计算机收集数据](../learn/quick-collect-linux-computer.md)
* [从混合 Windows 计算机收集数据](quick-collect-windows-computer.md)

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您选择本地安装并使用 PowerShell，本教程需要 Azure PowerShell Az 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-workspace"></a>创建工作区
创建与工作区[新建 AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 以下示例使用本地计算机上的资源管理器模板在 eastus 位置的资源组 Lab中创建名为 TestWorkspace 的工作区。 JSON 模板在经过配置后，只提示你输入工作区的名称，并为其他参数指定默认值，这些参数将会用作环境中的标准配置。 

有关支持的区域的信息，请参阅[Log Analytics 现已推出的区域](https://azure.microsoft.com/regions/services/)然后搜索从 Azure Monitor**搜索产品**字段。 

以下参数设置默认值：

* 位置 - 默认为“美国东部”
* sku - 默认为新的“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布

>[!WARNING]
>如果在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的新定价模型，则唯一有效的 Log Analytics 定价层为 **PerGB2018**。 
>

### <a name="create-and-deploy-template"></a>创建和部署模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 按要求编辑模板。 查看 [Microsoft.OperationalInsights/workspaces 模板](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces)参考，了解支持的属性和值。 
3. 在本地文件夹中将此文件另存为 **deploylaworkspacetemplate.json**。   
4. 已做好部署此模板的准备。 在包含模板的文件夹中使用以下命令：

    ```powershell
        New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deploylaworkspacetemplate.json
    ```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

![部署完成后的示例结果](media/quick-create-workspace-posh/template-output-01.png)

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。  

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[在 Azure Monitor 中收集要使用的 Azure 服务日志和指标](../platform/collect-azure-metrics-logs.md)。  
* [将 System Center Operations Manager 添加为数据源](../platform/om-agents.md)以从报告 Operations Manager 管理组的代理收集数据并将其存储在 Log Analytics 工作区中。  
* 连接 [Configuration Manager](../platform/collect-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[监视解决方案](../insights/solutions.md)以及如何从工作区添加或删除解决方案。
