---
title: 启用软删除 - Azure 文件共享
description: 了解如何在 Azure 文件共享上启用软删除，以进行数据恢复和防止意外删除。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 755ccd266b8a88471bf7dbc0d2deb790f35f3457
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883010"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>在 Azure 文件共享上启用软删除

Azure 存储为文件共享提供软删除，以便在应用程序或其他存储帐户用户错误地删除了数据后，可以更轻松地恢复数据。 若要了解有关软删除的详细信息，请参阅[如何防止意外删除 Azure 文件共享](storage-files-prevent-file-share-deletion.md)。

以下部分说明如何在现有存储帐户上为 Azure 文件共享启用和使用软删除：

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到存储帐户，然后在“文件服务”下选择“软删除”。
1. 为“文件共享软删除”选择“已启用”。
1. 选择“以天为单位的文件共享保持期”，然后输入选择的数字。
1. 选择“保存”以确认数据保留设置。

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="存储帐户软删除设置窗格的屏幕截图。突出显示文件共享部分，启用切换，设置保持期并保存。这将为你的存储帐户中的所有文件共享启用软删除。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要启用软删除，则必须更新文件客户端的服务属性。 下面的示例为存储帐户中的所有文件共享启用了软删除：

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

可以通过以下命令验证是否已启用软删除并查看其保留策略：

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>还原软删除的文件共享

# <a name="portal"></a>[门户](#tab/azure-portal)

若要还原软删除的文件共享：

1. 导航到存储帐户，然后选择“文件共享”。
1. 在“文件共享”边栏选项卡上，启用“显示删除的共享”显示已软删除的所有共享。

    这会显示当前处于“已删除”状态的所有共享。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="如果将名称列旁边的状态列设置为“已删除”，则文件共享处于软删除的状态。在指定的保持期后，它将被永久删除。":::

1. 选择共享，然后选择“取消删除”，这将还原共享。

    可以确认共享已还原，因为其状态切换为“活动”。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="如果将名称列旁边的状态列设置为“活动”，则文件共享已还原。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要还原软删除的文件共享，请使用以下命令：

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>禁用软删除

如果希望停止使用软删除，或永久删除文件共享，请按照以下说明操作：

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 导航到存储帐户，然后在“设置”下选择“软删除”。
1. 在“文件共享”下，为“软删除文件共享”选择“已禁用”。
1. 选择“保存”以确认数据保留设置。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="禁用软删除将允许在空闲时立即永久删除存储帐户中的所有文件共享。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可以使用以下命令在存储帐户上禁用软删除：

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---