---
title: 快速入门：使用 Azure 资源管理器模板部署 NSG 流日志
description: 了解如何使用 Azure 资源管理器模板和 Azure PowerShell 以编程方式启用 NSG 流日志。
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986311"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>快速入门：从 ARM 模板配置 NSG 流日志

此快速入门介绍使用[ Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)模板（ARM 模板）和 Azure PowerShell 以编程方式启用 [NSG 流日志](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 

首先，提供 NSG 流日志对象属性的概述，再提供一些示例模板。 然后，使用本地 PowerShell 实例部署模板。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="nsg-flow-logs-object"></a>NSG 流日志对象

下面显示了包含所有参数的 NSG 流日志对象。
有关属性的完整概述，可以阅读 [NSG 流日志模板参考](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs)。

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
若要创建 Microsoft.Network/networkWatchers/flowLogs 资源，请将以上 JSON 添加到模板的资源部分。


## <a name="creating-your-template"></a>创建模板

如果是首次使用 Azure 资源管理器模板，可以通过以下链接了解有关这些模板的详细信息。

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教程：创建和部署你的第一个 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)。

下面的完整模板示例是最简单的版本，通过最少的参数来设置 NSG 流日志。 有关更多示例，请转到此[链接](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)。

**示例**：以下模板启用了目标 NSG 上的 NSG 流日志，并将其存储在给定的存储帐户中。

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
> * 资源名称采用“Parent Resource_Child resource”格式。 在这里，父资源为区域网络观察程序实例（格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_centraluseuap）
> * targetResourceId 是目标 NSG 的资源 ID
> * storageId 是目标存储帐户的资源 ID


## <a name="deploying-your-azure-resource-manager-template"></a>部署 Azure 资源管理器模板

本教程假定你已有一个资源组和一个可以启用流登录的 NSG。
可以在本地将上述任何示例模板保存为 `azuredeploy.json`。 更新属性值，使其指向订阅中的有效资源。

若要部署模板，请在 PowerShell 中运行以下命令。
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上述命令会将资源部署到 NetworkWatcherRG 资源组，而不是包含 NSG 的资源组


## <a name="validate-the-deployment"></a>验证部署

可以通过多种方法来检查部署是否成功。 PowerShell 控制台应将“ProvisioningState”显示为“Succeeded”。 此外，还可以访问 [NSG 流日志门户页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)来确认所做的更改。 如果部署出现问题，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。

## <a name="deleting-your-resource"></a>删除资源
Azure 可通过“完整”部署模式删除资源。 若要删除流日志资源，请在“完整”模式下指定部署，而不包含要删除的资源。 详细了解[“完整”部署模式](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

此外，可以根据以下步骤在 Azure 门户中禁用 NSG 流日志：
1. 登录到 Azure 门户
2. 在门户左上角选择“所有服务”。 在“筛选器”框中，键入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
3. 在“日志”下，选择“NSG 流日志” 
4. 从 NSG 列表中，选择要为其禁用流日志的 NSG
5. 在“流日志设置”下，将流日志状态设置为“关闭” 
6. 向下滚动并选择“保存”

## <a name="next-steps"></a>后续步骤

在此快速入门中，你启用了 NSG 流日志。 现在，你必须了解如何使用以下工具对 NSG 流数据进行可视化： 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [打开源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
