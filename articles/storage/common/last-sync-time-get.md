---
title: 检查存储帐户的 "上次同步时间" 属性
titleSuffix: Azure Storage
description: 了解如何检查异地复制的存储帐户的 "**上次同步时间**" 属性。 "**上次同步时间**" 属性指示上次将主要区域中的所有写入成功写入次要区域的时间。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/16/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 3a406ce6db060b9ff5be7bcadecb6c7ff7e65a1f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165483"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>检查存储帐户的 "上次同步时间" 属性

在配置存储帐户时，可以指定将数据复制到主区域中数百英里的次要区域。 如果主要区域发生巨大中断（如自然灾害），异地复制为数据提供持久性。 如果你还启用对次要区域的读取访问权限，则当主要区域不可用时，你的数据将仍可用于读取操作。 如果主要区域无响应，可以将应用程序设计为无缝地从次要区域进行读取。

异地冗余存储（GRS）和异地冗余存储（GZRS）（预览版）都将数据异步复制到次要区域。 若要对次要区域进行读取访问，请启用读取访问异地冗余存储（GRS）或读取访问权限异地冗余存储（GZRS）。 有关 Azure 存储提供的冗余的各种选项的详细信息，请参阅[Azure 存储冗余](storage-redundancy.md)。

本文介绍如何检查存储帐户的 "**上次同步时间**" 属性，以便评估主要区域和次要区域之间的任何差异。

## <a name="about-the-last-sync-time-property"></a>关于上次同步时间属性

因为异地复制是异步的，所以在发生中断时，写入主要区域的数据可能尚未写入次要区域。 "**上次同步时间**" 属性指示上次将主要区域中的数据成功写入次要区域的时间。 在上次同步时间之前对主要区域进行的所有写入都可从辅助位置读取。 在上一次同步时间属性后对主要区域进行的写入操作可能会也可能不可用于读取。

"**上次同步时间**" 属性是 GMT 日期/时间值。

## <a name="get-the-last-sync-time-property"></a>获取上次同步时间属性

可以使用 PowerShell 或 Azure CLI 检索 "**上次同步时间**" 属性的值。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 获取存储帐户的上次同步时间，请安装支持获取异地复制统计信息的 Azure 存储预览模块。例如：

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
```

然后检查存储帐户的**GeoReplicationStats. LastSyncTime**属性。 请记住将占位符值替换为自己的值：

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要获取具有 Azure CLI 的存储帐户的上次同步时间，请检查存储帐户的**geoReplicationStats. lastSyncTime**属性。 使用 `--expand` 参数可以返回嵌套在**geoReplicationStats**下的属性的值。 请记住将占位符值替换为自己的值：

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
- [使用读取访问异地冗余存储设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)