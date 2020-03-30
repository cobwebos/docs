---
title: 检查存储帐户的"上次同步时间"属性
titleSuffix: Azure Storage
description: 了解如何检查异地复制存储帐户的 **"上次同步时间"** 属性。 "**上次同步时间"** 属性指示从主区域成功写入辅助区域的所有写入的最后一次。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165483"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>检查存储帐户的"上次同步时间"属性

配置存储帐户时，可以指定将数据复制到距主区域数百英里的辅助区域。 在主区域发生重大中断（如自然灾害）时，异地复制可为数据提供持久性。 如果另外启用对辅助区域的读取访问，则如果主区域不可用，您的数据仍可用于读取操作。 如果主区域无响应，则可以设计应用程序以无缝切换到从辅助区域读取。

异地冗余存储 （GRS） 和地理区域冗余存储 （GZRS） （预览）都异步地将数据复制到辅助区域。 对于对辅助区域的读取访问，启用读取访问异地冗余存储 （RA-GRS） 或读取访问地理区域冗余存储 （RA-GZRS）。 有关 Azure 存储提供的冗余的各种选项的详细信息，请参阅 Azure[存储冗余](storage-redundancy.md)。

本文介绍如何检查存储帐户**的"上次同步时间"** 属性，以便可以评估主区域和辅助区域之间的任何差异。

## <a name="about-the-last-sync-time-property"></a>关于上次同步时间属性

由于异地复制是异步的，因此在发生中断时，可能尚未将写入主区域的数据写入辅助区域。 "**上次同步时间"** 属性指示上次成功将主区域的数据写入辅助区域。 在最后一个同步时间之前对主区域进行的所有写入都可以从辅助位置读取。 上次同步时间之后对主区域进行的写入属性可能还不能读取，也可能不可用。

**"上次同步时间"** 属性是 GMT 日期/时间值。

## <a name="get-the-last-sync-time-property"></a>获取上次同步时间属性

可以使用 PowerShell 或 Azure CLI 检索 **"上次同步时间"** 属性的值。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

要获取使用 PowerShell 的存储帐户的最后一次同步时间，请安装支持获取异地复制统计信息的 Azure 存储预览模块。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然后检查存储帐户的 **GeoReplicationStats.LastSyncTime** 属性。 请务必将占位符值替换为你自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要获取使用 Azure CLI 的存储帐户的最后一次同步时间，请检查存储帐户的**地理复制Stats.lastSyncTime**属性。 使用 `--expand` 参数返回在 **geoReplicationStats** 下嵌套的属性的值。 请务必将占位符值替换为你自己的值：

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [更改存储帐户的冗余选项](redundancy-migration.md)
- [使用读取访问异地冗余存储设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)