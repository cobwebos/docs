---
title: 升级 Azure Service Fabric 群集的配置
description: 了解如何使用资源管理器模板升级在 Azure 中运行的 Service Fabric 群集的配置。
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842710"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>在 Azure 中升级群集的配置 

本文介绍如何为 Service Fabric 群集自定义各种结构设置。 对于 Azure 中托管的群集，可以通过 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板自定义设置。

> [!NOTE]
> 并非所有设置在门户中皆可用，[最佳做法是使用 Azure 资源管理器模板对其进行自定义](./service-fabric-best-practices-infrastructure-as-code.md)；门户仅适用于 Service Fabric 开发\测试方案。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用资源管理器模板自定义群集设置
可以通过 JSON 资源管理器模板配置 Azure 群集。 若要了解有关不同设置的详细信息，请参阅[群集的配置设置](service-fabric-cluster-fabric-settings.md)。 例如，下面的步骤演示如何使用 Azure 资源浏览器将新设置 MaxDiskQuotaInMB 添加到“诊断”部分****。

1. 转到 https://resources.azure.com
2. 通过展开**订阅**  ->  **\<Your Subscription>**  ->  **resourceGroups**  ->  **\<Your Resource Group>**  ->  **提供程序**  ->  **ServiceFabric**  ->  **群集**导航到订阅 -> **\<Your Cluster Name>**
3. 在右上角，选择 " **读/写"。**
4. 选择 " **编辑** "，更新 `fabricSettings` JSON 元素并添加新元素：

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

- 使用 [Azure 门户](../azure-resource-manager/templates/export-template-portal.md)导出和更新资源管理器模板。
- 使用 [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) 导出和更新资源管理器模板。
- 使用 [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) 导出和更新资源管理器模板。
- 使用 Azure PowerShell [AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) 和 [AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) 命令直接修改设置。
- 使用 Azure CLI [az sf cluster setting](/cli/azure/sf/cluster/setting) 命令直接修改这些设置。

## <a name="next-steps"></a>后续步骤
* 了解 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)。
* 了解如何 [扩展和缩减群集](service-fabric-cluster-scale-in-out.md)。
