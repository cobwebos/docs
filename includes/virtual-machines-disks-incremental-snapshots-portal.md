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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80628424"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>区域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>门户


1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到要拍摄快照的磁盘。
1. 在磁盘上，选择“创建快照”****

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="屏幕截图。磁盘的边栏选项卡，其中突出显示了“+创建快照”****，因为这是必选的。":::

1. 选择要使用的资源组并输入名称。
1. 选择“增量”，然后选择“查看 + 创建”**** ****

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="屏幕截图。磁盘的边栏选项卡，其中突出显示了“+创建快照”****，因为这是必选的。":::

1. 选择“创建”****

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="屏幕截图。磁盘的边栏选项卡，其中突出显示了“+创建快照”****，因为这是必选的。":::

## <a name="next-steps"></a>后续步骤

要查看演示使用 .NET 的增量快照的差异功能的示例代码，请参阅[通过增量快照差异功能将 Azure 托管磁盘备份复制到另一区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
