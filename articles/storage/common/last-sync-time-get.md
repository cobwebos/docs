---
title: 检查存储帐户的“上次同步时间”属性
titleSuffix: Azure Storage
description: 了解如何检查异地复制的存储帐户的“上次同步时间”属性。 “上次同步时间”属性指示上一次将主要区域中的所有写入内容成功写入到次要区域的时间。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 4309f1dc63ac7dd96e22f4564a32aae6ed59ad84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195804"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>检查存储帐户的“上次同步时间”属性

在配置存储帐户时，可以指定将数据复制到距离主要区域数百英里的次要区域。 如果主要区域发生重大故障（如自然灾害），异地复制可提供数据的持续性。 如果还启用了对次要区域的读取访问，则当主要区域不可用时，仍可对数据执行读取操作。 可将应用程序设计为，在主要区域无响应时，无缝转换为从次要区域进行读取。

异地冗余存储 (GRS) 和地理区域冗余存储 (GZRS) 都会将数据异步复制到次要区域。 若要对次要区域进行读取访问，可启用读取访问异地冗余存储 (RA-GRS) 或读取访问地理区域冗余存储 (RA-GZRS)。 有关 Azure 存储提供的各种冗余选项的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

本文介绍如何检查存储帐户的“上次同步时间”属性，以便可以评估主要区域和次要区域之间的任何差异。

## <a name="about-the-last-sync-time-property"></a>关于“上次同步时间”属性

由于异地复制是异步的，因此在发生中断时，写入主要区域的数据可能尚未被写入到次要区域。 “上次同步时间”属性指示上次将主要区域中的数据成功写入到次要区域的时间。 在上次同步时间之前对主要区域进行的所有写入都可以从次要位置进行读取。 在上次同步时间属性之后对主要区域进行的写入不一定可供读取。

“上次同步时间”属性是 GMT 日期/时间值。

## <a name="get-the-last-sync-time-property"></a>获取“上次同步时间”属性

可以使用 PowerShell 或 Azure CLI 检索“上次同步时间”属性的值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要获取具有 PowerShell 的存储帐户的上次同步时间，请安装[1.11.0 模块的](https://www.powershellgallery.com/packages/Az.Storage)版本或更高版本。 然后检查存储帐户的 GeoReplicationStats.LastSyncTime 属性。 请务必将占位符值替换为你自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 获取存储帐户的上次同步时间，请检查存储帐户的 geoReplicationStats.lastSyncTime 属性。 使用 `--expand` 参数返回嵌套在 geoReplicationStats 下的属性的值。 请务必将占位符值替换为你自己的值：

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>另请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [更改存储帐户的冗余选项](redundancy-migration.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
