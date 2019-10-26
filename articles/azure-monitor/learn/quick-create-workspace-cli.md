---
title: 使用 Azure CLI 创建 Log Analytics 工作区 | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.openlocfilehash: 18bfc99ded6e3e9171fbb20fbf329700817829de
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894997"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Log Analytics 工作区

Azure CLI 2.0 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure CLI 2.0 在 Azure Monitor 中部署 Log Analytics 工作区。 Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。 如果打算从以下源收集数据，则需要 Log Analytics 工作区：

* 订阅中的 Azure 资源  
* 受 System Center Operations Manager 监视的本地计算机  
* System Center Configuration Manager 中的设备集合  
* Azure 存储中的诊断或日志数据  
 
对于其他源，如环境中的 Azure VM 和 Windows VM 或 Linux VM，请参阅以下主题：

* [从 Azure 虚拟机收集数据](../learn/quick-collect-azurevm.md)
* [从混合 Linux 计算机收集数据](../learn/quick-collect-linux-computer.md)
* [从混合 Windows 计算机收集数据](quick-collect-windows-computer.md)

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.30 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-workspace"></a>创建工作区
使用 [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 创建工作区。 下面的示例使用本地计算机中的资源管理器模板在*eastus*位置创建一个工作区。 JSON 模板在经过配置后，只提示你输入工作区的名称，并为其他参数指定默认值，这些参数将会用作环境中的标准配置。 也可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的更多信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)

有关支持的区域的信息，请参阅[中提供 Log Analytics 区域](https://azure.microsoft.com/regions/services/)，并从 "**搜索产品**" 字段搜索 Azure Monitor。 

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
4. 已做好部署此模板的准备。 在包含模板的文件夹中使用以下命令。 如果系统提示输入工作区名称，请提供在所有 Azure 订阅中全局唯一的名称。

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

![部署完成后的示例结果](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。  

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[收集要在 Log Analytics 中使用的 Azure 服务日志和指标](../platform/collect-azure-metrics-logs.md)。  
* [将 System Center Operations Manager 添加为数据源](../platform/om-agents.md)以从报告 Operations Manager 管理组的代理收集数据并将其存储在 Log Analytics 工作区中。  
* 连接 [Configuration Manager](../platform/collect-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[监视解决方案](../insights/solutions.md)以及如何从工作区添加或删除解决方案。
