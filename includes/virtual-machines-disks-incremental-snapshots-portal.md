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
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300227"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>区域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>门户

若要在全局 Azure 门户中创建和配置增量快照，必须使用以下链接： [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)。 增量快照创建在全局 Azure 门户中尚不可用。

1. 用提供的链接登录到[Azure 门户](https://aka.ms/incrementalsnapshots)，并导航到想要快照的磁盘。
1. 在磁盘上，选择 "**创建快照**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. 选择要使用的资源组，并输入名称。
1. 选择 "**增量**"，然后选择 "**查看 + 创建**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. 选择“创建”

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>后续步骤

若要查看演示增量快照的差异功能的示例代码，请参阅使用 .NET，请参阅[将 Azure 托管磁盘备份复制到另一个区域，其差异功能为增量快照](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
