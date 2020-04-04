---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628424"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>区域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>门户


1. 登录到[Azure 门户](https://portal.azure.com/)并导航到要快照的磁盘。
1. 在磁盘上，选择 **"创建快照**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截图。磁盘的边栏选项卡，突出显示 [创建快照]，因为您必须选择。":::

1. 选择要使用的资源组并输入名称。
1. 选择**增量**并选择 **"查看 + 创建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截图。创建快照边栏选项卡，填写名称并选择增量，然后创建快照。":::

1. 选择 **"创建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截图。验证快照的验证页，确认您的选择，然后创建快照。":::

## <a name="next-steps"></a>后续步骤

如果要查看示例代码，演示使用 .NET 的增量快照的差分功能，请参阅[将 Azure 托管磁盘备份复制到具有增量快照差异功能的另一个区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
