---
title: 升级 Azure Service Fabric 群集的配置
description: 了解如何使用资源管理器模板升级在 Azure 中运行的 Service Fabric 群集的配置。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463287"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升级群集的配置 

本文介绍如何为 Service Fabric 群集自定义各种结构设置。 对于 Azure 中托管的群集，可以通过 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板自定义设置。

> [!NOTE]
> 并非所有设置在门户中皆可用，[最佳做法是使用 Azure 资源管理器模板对其进行自定义](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)；门户仅适用于 Service Fabric 开发\测试方案。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用资源管理器模板自定义群集设置
可以通过 JSON 资源管理器模板配置 Azure 群集。 若要了解有关不同设置的详细信息，请参阅[群集的配置设置](service-fabric-cluster-fabric-settings.md)。 例如，下面的步骤演示如何使用 Azure 资源浏览器将新设置 MaxDiskQuotaInMB 添加到“诊断”部分****。

1. 转到 https://resources.azure.com
2. 若要导航**到订阅，请展开** -> **\<订阅>**  ->  **resourceGroups** -> **\<资源组中的资源组>**  -> **提供程序** -> **Microsoft. ServiceFabric** -> **群集** -> **\<群集名称>**
3. 在右上角，选择 "**读/写"。**
4. 选择 "**编辑**" `fabricSettings` ，更新 JSON 元素并添加新元素：

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
- 使用 Azure PowerShell [AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting)和[AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting)命令直接修改设置。
- 使用 Azure CLI [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) 命令直接修改这些设置。

## <a name="next-steps"></a>后续步骤
* 了解 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)。
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-up-down.md)。
