---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486142"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>区域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>门户

要在全局 Azure 门户中创建和配置增量快照，必须使用以下链接： [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)。 增量快照创建在全局 Azure 门户中尚不可用。

1. 使用提供的链接登录到[Azure 门户](https://aka.ms/incrementalsnapshots)，然后导航到要快照的磁盘。
1. 在磁盘上，选择 **"创建快照**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截图。磁盘的边栏选项卡，突出显示 [创建快照]，因为您必须选择。":::

1. 选择要使用的资源组并输入名称。
1. 选择**增量**并选择 **"查看 + 创建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截图。创建快照边栏选项卡，填写名称并选择增量，然后创建快照。":::

1. 选择 **"创建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截图。验证快照的验证页，确认您的选择，然后创建快照。":::

## <a name="next-steps"></a>后续步骤

如果要查看示例代码，演示使用 .NET 的增量快照的差分功能，请参阅[将 Azure 托管磁盘备份复制到具有增量快照差异功能的另一个区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
