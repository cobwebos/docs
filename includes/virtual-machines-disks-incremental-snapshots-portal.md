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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80628424"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>区域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>门户


1. 登录到[Azure 门户](https://portal.azure.com/)并导航到要进行快照的磁盘。
1. 在磁盘上，选择 "**创建快照**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="图.磁盘的边栏选项卡上突出显示了 * * + Create snapshot * *，就是您必须选择的选项。":::

1. 选择要使用的资源组，并输入名称。
1. 选择 "**增量**"，然后选择 "**查看 + 创建**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="图."创建快照" 边栏选项卡，填入名称并选择 "增量"，然后创建快照。":::

1. 选择**创建**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="图.快照的 "验证" 页，确认所做的选择，然后创建快照。":::

## <a name="next-steps"></a>后续步骤

若要查看演示增量快照的差异功能的示例代码，请参阅使用 .NET，请参阅[将 Azure 托管磁盘备份复制到另一个区域，其差异功能为增量快照](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
