---
title: 升级 Azure Service Fabric 群集的配置 | Microsoft Docs
description: 了解如何使用资源管理器模板升级在 Azure 中运行的 Service Fabric 群集的配置。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 9323b393edb808f3d2d069f868deb0b67cd0c871
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51857882"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升级群集的配置 

本文介绍如何为 Service Fabric 群集自定义各种结构设置。 对于 Azure 中托管的群集，可以通过 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板自定义设置。

> [!NOTE]
> 并非所有设置在门户中皆可用。 如果不能通过门户使用下面列出的设置，请使用 Azure 资源管理器模板对其进行自定义。> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用资源管理器模板自定义群集设置
可以通过 JSON 资源管理器模板配置 Azure 群集。 若要了解有关不同设置的详细信息，请参阅[群集的配置设置](service-fabric-cluster-fabric-settings.md)。 例如，下面的步骤演示如何使用 Azure 资源浏览器将新设置 MaxDiskQuotaInMB 添加到“诊断”部分。

1. 转到 https://resources.azure.com
2. 通过展开“订阅” -> \<你的订阅> -> “resourceGroups” -> \<你的资源组> -> “提供程序” -> “Microsoft.ServiceFabric” -> “群集” -> \<你的群集名称>导航到你的订阅
3. 选择右上角的“读/写”。
4. 选择“编辑”，更新 `fabricSettings` JSON 元素并添加新元素：

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

此外，还可以使用 Azure 资源管理器通过以下任一方式自定义群集设置：

- 使用 [Azure 门户](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)导出和更新资源管理器模板。
- 使用 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) 导出和更新资源管理器模板。
- 使用 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) 导出和更新资源管理器模板。
- 使用 Azure RM PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) 和 [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) 命令直接修改这些设置。
- 使用 Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) 命令直接修改这些设置。

## <a name="next-steps"></a>后续步骤
* 了解 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)。
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-up-down.md)。
