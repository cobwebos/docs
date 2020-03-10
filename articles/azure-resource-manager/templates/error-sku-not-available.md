---
title: SKU 不可用错误
description: 介绍如何在使用 Azure 资源管理器部署资源时排除 "SKU 不可用" 错误。
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 3dcc26f2d74799a6d282ee4bd733d36bec7b05e4
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942725"
---
# <a name="resolve-errors-for-sku-not-available"></a>解决 SKU 不可用的错误

本文介绍如何解决 SkuNotAvailable 错误。 如果在该区域/区域中找不到合适的 SKU 或满足业务需求的备用区域/区域，请将[SKU 请求](https://aka.ms/skurestriction)提交到 Azure 支持。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

在部署资源（通常为虚拟机）时，收到以下错误代码和错误消息：

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

当所选的资源 SKU（如 VM 大小）不可用于所选的位置时，会收到此错误。

如果要部署 Azure 点 VM 或点规模集实例，此位置中的 Azure 位置没有任何容量。 有关详细信息，请参阅[污点错误消息](../../virtual-machines/error-codes-spot.md)。

## <a name="solution-1---powershell"></a>解决方案 1 - PowerShell

若要确定区域/区域中可用的 Sku，请使用[AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku)命令。 按位置筛选结果。 必须拥有最新版本 PowerShell 才能运行此命令。

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

结果包括位置的 SKU 列表以及针对该 SKU 的任何限制。 请注意，SKU 可能被列为 `NotAvailableForSubscription`。

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

一些其他示例：

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

在末尾追加 "fc" 会返回更多详细信息。

## <a name="solution-2---azure-cli"></a>解决方案 2 - Azure CLI

要确定区域中可用的 SKU，请使用 `az vm list-skus` 命令。 使用 `--location` 参数将输出筛选到正在使用的位置。 使用 `--size` 参数按部分大小名称搜索。

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

该命令将返回类似下面的结果：

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>解决方案 3 - Azure 门户

要确定区域中可用的 SKU，请使用[门户](https://portal.azure.com)。 登录到门户，并通过接口添加资源。 设置值时，可看到该资源的可用 SKU。 不需要完成部署。

例如，开始创建虚拟机的过程。 若要查看其他可用的大小，请选择“更改大小”。

![创建 VM](./media/error-sku-not-available/create-vm.png)

可以筛选和滚动到可用的大小。

![可用的 SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>解决方案 4 - REST

要确定区域中可用的 SKU，请使用[资源 Sku - 列表](/rest/api/compute/resourceskus/list)操作。

它会以下列格式返回可用 SKU 和区域：

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

