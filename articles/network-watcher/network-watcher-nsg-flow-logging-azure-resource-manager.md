---
title: 网络观察程序 - 使用 Azure 资源管理器模板创建 NSG 流日志
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
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891452"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>从 Azure 资源管理器模板配置 NSG 流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure 资源管理器](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)是 Azure 作为[代码管理基础结构](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)的本机和强大方法。

本文演示如何使用 Azure 资源管理器模板和 Azure PowerShell 以编程方式启用[NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 我们首先提供 NSG 流日志对象属性的概述，然后是几个示例模板。 然后，我们使用本地 PowerShell 实例部署模板。


## <a name="nsg-flow-logs-object"></a>NSG 流日志对象

具有所有参数的 NSG 流日志对象如下所示。
有关属性的完整概述，请阅读[NSG 流日志模板引用](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)。

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
要创建 Microsoft.Network/网络观察程序/流日志资源，请将上述 JSON 添加到模板的资源部分。


## <a name="creating-your-template"></a>创建模板

如果首次使用 Azure 资源管理器模板，则可以使用以下链接了解有关这些模板的更多内容。

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教程：创建并部署第一个 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


下面是两个用于设置 NSG 流日志的完整模板示例。

**示例 1**： 上述最简单的版本，传递最小参数。 以下模板支持目标 NSG 上的 NSG 流日志，并将它们存储在给定的存储帐户中。

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
> * 资源的名称具有"父资源>/子资源"的格式。 此处，父资源是区域网络观察程序实例（格式：NetworkWatcher_<RegionName>。 示例：NetworkWatcher_centraluseuap）
> * 目标资源 Id 是目标 NSG 的资源 ID
> * 存储 Id 是目标存储帐户的资源 ID

**示例 2**： 以下模板启用 NSG 流日志（版本 2），保留 5 天。 启用处理间隔为 10 分钟的流量分析。

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
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>部署 Azure 资源管理器模板

本教程假定您具有现有资源组和可以启用流登录的 NSG。
您可以将上述任何示例模板保存在本地为`azuredeploy.json`。 更新属性值，以便它们指向订阅中的有效资源。

要部署模板，在 PowerShell 中运行以下命令。
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>验证部署

有几种方法可以检查部署是否成功。 您的 PowerShell 控制台应将"预配状态"显示为"成功"。 此外，您还可以访问[NSG 流日志门户页面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)以确认您的更改。 如果部署出现问题，请查看[Azure 资源管理器中常见的 Azure 部署错误故障排除](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。

## <a name="deleting-your-resource"></a>删除资源
Azure 通过"完成"部署模式启用资源删除。 要删除流日志资源，请在"完成"模式下指定部署，而不包括要删除的资源。 阅读有关["完成部署模式"的更多内容](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>后续步骤

了解如何使用：
* [微软电源 BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [开源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
