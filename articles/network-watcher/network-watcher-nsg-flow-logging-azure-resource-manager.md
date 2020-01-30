---
title: 网络观察程序-使用 Azure 资源管理器模板创建 NSG 流日志
description: 使用 Azure 资源管理器模板和 PowerShell 轻松设置 NSG 流日志。
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 2ae6314accf5ad9143e1e103239c2e7c884ec947
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776633"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>配置 Azure 资源管理器模板中的 NSG 流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure 资源管理器](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)是 azure 的本机且功能强大的方法，可将[基础结构作为代码](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)进行管理。

本文介绍如何使用 Azure 资源管理器模板和 Azure PowerShell 以编程方式启用[NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 首先，提供 NSG Flow 日志对象属性的概述，并提供一些示例模板。 然后，使用本地 PowerShell 实例部署模板。


## <a name="nsg-flow-logs-object"></a>NSG Flow 日志对象

下面显示了包含所有带参数的 NSG Flow 日志对象。
有关属性的完整概述，可以阅读[NSG Flow 日志模板引用](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)。

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
若要创建 networkWatchers/flowLogs 资源，请将以上 JSON 添加到模板的 resources 节。


## <a name="creating-your-template"></a>创建模板

如果是首次使用 Azure 资源管理器模板，可以通过以下链接了解有关这些模板的详细信息。

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教程：创建和部署第一个 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


下面是用于设置 NSG 流日志的完整模板的两个示例。

**示例 1**：最小版本的上述，其中传递了最小参数。 以下模板启用目标 NSG 上的 NSG 流日志，并将它们存储在给定的存储帐户中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * 资源名称的格式为 "父资源 >/Child 资源"。 此处的父资源是区域网络观察程序实例（格式： NetworkWatcher_<RegionName>。 示例： NetworkWatcher_centraluseuap）
> * targetResourceId 是目标 NSG 的资源 ID
> * storageId 是目标存储帐户的资源 ID

**示例 2**：以下模板启用 NSG Flow 日志（版本2），保留期为5天。 启用流量分析，处理间隔为10分钟。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>部署 Azure 资源管理器模板

本教程假定你已有一个资源组和一个 NSG 可以启用流登录。
你可以在本地将上述任何示例模板保存为 `azuredeploy.json`。 更新属性值，使其指向订阅中的有效资源。

若要部署模板，请在 PowerShell 中运行以下命令。
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>验证你的部署

可以通过多种方法来检查部署是否成功。 PowerShell 控制台应将 "ProvisioningState" 显示为 "Succeeded"。 此外，还可以访问[NSG Flow 日志门户页面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)，确认所做的更改。 如果部署出现问题，请参阅[使用 azure 资源管理器排查常见的 azure 部署错误](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。


## <a name="next-steps"></a>后续步骤

了解如何使用可视化 NSG 流数据：
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [开源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
