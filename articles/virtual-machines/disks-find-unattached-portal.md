---
title: 标识未附加的 Azure 磁盘 - Azure门户
description: 如何使用 Azure 门户查找未附加的 Azure 托管和非托管（VHD/页 blob）磁盘。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6632d65fa07788e35b24c2f957e713f824f6b091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87542732"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>查找并删除未附加的 Azure 托管和非托管磁盘 - Azure 门户

删除 Azure 中的虚拟机 (VM) 时，默认不删除附加到 VM 的任何磁盘。 这有助于防止意外删除 VM 而导致的数据丢失。 删除 VM 后，可继续支付未附加的磁盘。 本文演示了如何使用 Azure 门户查找并删除任何未附加的磁盘，以及如何减少不必要的成本。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>托管磁盘：查找并删除未附加的磁盘

如果有未附加的托管磁盘，并且不再需要这些磁盘上的数据，可以参照以下过程从 Azure 门户中查找这些磁盘：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索并选择“磁盘”。

    “磁盘”边栏选项卡上会显示所有磁盘的列表。 “所有者”列中含有“-”的任何磁盘都是未附加的磁盘 。

    [!["托管磁盘" 边栏选项卡的屏幕截图，如果磁盘在 "所有者" 列中，则它是一个未附加的磁盘。](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. 选择要删除的未附加磁盘，随即会打开磁盘的边栏选项卡。
1. 在磁盘的边栏选项卡上，确认磁盘状态为未附加，然后选择“删除”。

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="单个托管磁盘边栏选项卡的屏幕截图。如果磁盘未附加，此边栏选项卡中的磁盘状态会显示为“未附加”。如果不再需要保留该磁盘的数据，可以删除该磁盘":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>非托管磁盘：查找并删除未附加的磁盘

非托管磁盘是指以[页 blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 形式存储在 [Azure 存储帐户](../storage/common/storage-account-overview.md)中的 VHD 文件。

如果有未附加到 VM 的非托管磁盘，并且不再需要这些磁盘上的数据，要删除它们，可参照以下过程从 Azure 门户中执行相应操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索并选择“磁盘（经典）”。

    之后会显示所有非托管磁盘的列表。 其“附加到”列中包含“-”的任何磁盘都是未附加的磁盘 。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="单个托管磁盘边栏选项卡的屏幕截图。如果磁盘未附加，此边栏选项卡中的磁盘状态会显示为“未附加”。如果不再需要保留该磁盘的数据，可以删除该磁盘":::

1. 选择要删除的未附加磁盘，随即会显示磁盘的边栏选项卡。

1. 在磁盘的边栏选项卡上，可以确认它是未附加的，因为“附加到”仍显示为“-” 。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="单个托管磁盘边栏选项卡的屏幕截图。如果磁盘未附加，此边栏选项卡中的磁盘状态会显示为“未附加”。如果不再需要保留该磁盘的数据，可以删除该磁盘":::

1. 选择“删除”。

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="单个托管磁盘边栏选项卡的屏幕截图。如果磁盘未附加，此边栏选项卡中的磁盘状态会显示为“未附加”。如果不再需要保留该磁盘的数据，可以删除该磁盘":::

## <a name="next-steps"></a>后续步骤

如果要自动查找和删除未附加的存储帐户，请参阅 [CLI](linux/find-unattached-disks.md) 或 [PowerShell](windows/find-unattached-disks.md) 文章。

有关详细信息，请参阅[删除存储帐户](../storage/common/storage-account-create.md#delete-a-storage-account)和[使用 PowerShell 标识孤立磁盘](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)
