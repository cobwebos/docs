---
title: 对块 blob 数据执行时间点还原
titleSuffix: Azure Storage
description: 了解如何使用时间点还原在给定的时间点将一组块 blob 还原到其以前的状态。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 226e35452e4b266c3c0a698505d47ab9a53b9761
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984376"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>对块 blob 数据执行时间点还原

可以使用时间点还原将一个或多个块 blob 集还原到以前的状态。 本文介绍如何为存储帐户启用时间点还原，以及如何执行还原操作。

若要了解有关时间点还原的详细信息，请参阅 [块 blob 的时间点还原](point-in-time-restore-overview.md)。

> [!CAUTION]
> 时间点还原仅支持对块 blob 执行还原操作。 无法还原对容器的操作。 如果通过调用 [删除容器](/rest/api/storageservices/delete-container) 操作从存储帐户中删除容器，则无法使用还原操作来还原该容器。 如果你可能希望还原各个 blob，则不会删除容器。

## <a name="enable-and-configure-point-in-time-restore"></a>启用和配置时间点还原

在启用和配置时间点还原之前，请为存储帐户启用其先决条件：软删除、更改源和 blob 版本控制。 若要详细了解如何启用上述每项功能，请参阅以下文章：

- [为 blob 启用软删除](soft-delete-enable.md)
- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

> [!IMPORTANT]
> 启用软删除、更改源和 blob 版本控制可能会产生额外费用。 有关详细信息，请参阅 [blob 的软删除](soft-delete-blob-overview.md)、 [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)和 [Blob 版本控制](versioning-overview.md)。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要配置 Azure 门户的时间点还原，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在 " **设置**" 下，选择 " **数据保护**"。
1. 选择 **"打开时间点还原"** 。 如果选择此选项，则还会启用 blob、版本控制和更改源的软删除。
1. 设置以天为单位的时间点还原的最大还原点。 此数字必须至少为一天，小于为 blob 软删除指定的保持期。
1. 保存所做更改。

下图显示了为时间点还原配置的存储帐户，其中的还原点为7天前，并显示了14天的 blob 软删除保留期。

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="显示如何在 Azure 门户中配置时间点还原的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 配置时间点还原，请首先安装 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) module 版本2.6.0 或更高版本。 然后调用 AzStorageBlobRestorePolicy 命令为存储帐户启用时间点还原。

以下示例启用软删除并设置软删除保留期，启用更改源，然后启用时间点还原。 运行此示例之前，还需要使用 Azure 门户或 Azure 资源管理器模板来启用 blob 版本控制。

运行此示例时，请务必将尖括号中的值替换为你自己的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>执行还原操作

执行还原操作时，必须将还原点指定为 UTC **日期时间** 值。 容器和 blob 将还原到该日期和时间的状态。 还原操作可能需要几分钟才能完成。

可以还原存储帐户中的所有容器，也可以还原一个或多个容器中的一系列 blob。 Blob 的范围是按字典顺序定义的，这意味着按字典顺序。 每个还原操作最多支持10个字典范围。 范围的起始值为 "包含"，范围的结尾为 "独占"。

为开始范围和结束范围指定的容器模式必须包含至少三个字符（即最小值为 3）。 用于分隔容器名称与 blob 名称的正斜杠 (/) 不计入该最小值。

字典范围不支持通配符。 任何通配符字符都将被视为标准字符。

可以还原 `$root` 和 `$web` 容器中的 blob，方法是：在传递给还原操作的范围中显式指定它们。 `$root` 和 `$web` 容器只有在显式指定的情况下才会进行还原。 无法还原其他系统容器。

仅还原块 blob。 还原操作中不包含页 blob 和追加 blob。 有关与追加 blob 相关的限制的详细信息，请参阅 [块 blob 的时间点还原](point-in-time-restore-overview.md)。

> [!IMPORTANT]
> 执行还原操作时，Azure 存储会阻止在操作期间还原的范围内的 blob 上的数据操作。 读取、写入和删除操作在主位置中被阻止。 出于此原因，在执行还原操作时，Azure 门户中的操作（如列出容器）可能不会按预期执行。
>
> 如果存储帐户是异地复制的，则在还原操作期间，从辅助位置读取操作可能会继续。

### <a name="restore-all-containers-in-the-account"></a>还原帐户中的所有容器

可以还原存储帐户中的所有容器，使其在给定的时间点恢复为其以前的状态。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要将存储帐户中的所有容器和 blob 还原 Azure 门户，请执行以下步骤：

1. 导航到存储帐户的容器列表。
1. 在工具栏上，选择 " **还原容器**"，然后选择 " **全部还原**"。
1. 在 " **还原所有容器** " 窗格中，通过提供日期和时间来指定还原点。
1. 通过选中框确认是否要继续。
1. 选择 " **还原** " 以开始还原操作。

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="屏幕截图，显示如何将所有容器还原到指定的还原点":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 还原存储帐户中的所有容器和 blob，请调用 **AzStorageBlobRange** 命令。 默认情况下， **AzStorageBlobRange** 命令以异步方式运行，并返回 **PSBlobRestoreStatus** 类型的对象，您可以使用该对象来检查还原操作的状态。

下面的示例将存储帐户中的容器异步还原到当前时间之前的12小时，并检查还原操作的某些属性：

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

若要同步运行还原操作，请在命令中包含 **-WaitForComplete** 参数。 存在 **-WaitForComplete** 参数时，PowerShell 会显示一条消息，其中包含操作的还原 ID，然后在还原操作完成前阻止执行。 请记住，还原操作所需的时间长度取决于要还原的数据量，较大的还原操作可能需要长达一小时才能完成。

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

---

### <a name="restore-ranges-of-block-blobs"></a>还原块 blob 的范围

您可以在一个容器或多个容器中还原一个或多个字典范围内的 blob，以在给定的时间点将这些 blob 返回到它们以前的状态。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户还原一个或多个容器中的某个范围的 blob，请执行以下步骤：

1. 导航到存储帐户的容器列表。
1. 选择要还原的容器。
1. 在工具栏上，选择 " **还原容器**"，然后选择 " **还原**"。
1. 在 " **还原所选容器** " 窗格中，通过提供日期和时间来指定还原点。
1. 指定要还原的范围。 使用正斜杠 (/) 来描绘 blob 前缀中的容器名称。
1. 默认情况下，" **还原所选容器** " 窗格指定包含容器中所有 blob 的范围。 如果你不想还原整个容器，则删除此范围。 下图显示了默认范围。

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="显示在指定自定义范围之前要删除的默认 blob 范围的屏幕截图":::

1. 通过选中框确认是否要继续。
1. 选择 " **还原** " 以开始还原操作。

下图显示了对一组范围的还原操作。

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="显示如何在一个或多个容器中还原 blob 范围的屏幕截图":::

映像中显示的还原操作执行以下操作：

- 还原 *container1*的完整内容。
- 在*container2*的字典范围*blob1*到*blob5*中还原 blob。 此范围将还原名称为 *blob1*、 *blob11*、 *blob100*、 *blob2*等的 blob。 由于范围的结尾是独占的，因此它会还原名称以 *blob4*开头的 blob，但不会还原名称以 *blob5*开头的 blob。
- 还原 *container3* 和 *container4*中的所有 blob。 因为范围的结尾是独占的，所以此范围不会还原 *container5*。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要还原单个范围的 blob，请调用 **AzStorageBlobRange** 命令，并为参数指定字典和 blob 名称的字典范围 `-BlobRestoreRange` 。 例如，若要还原名为 *container1*的单个容器中的 blob，可以指定一个以 *container1* 开头的范围，并以 *container2*结尾。 不要求在开始和结束范围中指定的容器存在。 因为范围的结尾是独占的，即使存储帐户包含名为 *container2*的容器，也只会还原名为 *container1* 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

若要指定要还原的容器中的 blob 子集，请使用正斜杠 (/) 将容器名称与 blob 前缀模式分隔开。 例如，下面的范围选择单个容器中名称以字母 *d* 到 *f* 开头的 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

接下来，为 **AzStorageBlobRange** 命令提供范围。 通过为 `-TimeToRestore` 参数提供一个 UTC **日期/时间**值来指定还原点。 下面的示例将指定范围内的 blob 还原到当前时间后推 3 天的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

默认情况下， **AzStorageBlobRange** 命令以异步方式运行。 异步启动还原操作时，PowerShell 会立即显示此操作的属性表：  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

若要还原多个范围的块 blob，请为 `-BlobRestoreRange` 参数指定一个范围数组。 下面的示例指定两个范围，以将 *container1* 和 *container4* 的完整内容还原到其状态24小时之前，并将结果保存到变量：

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

若要同步运行还原操作并在执行完成后阻止执行，请在命令中包含 **-WaitForComplete** 参数。

---

## <a name="next-steps"></a>后续步骤

- [块 blob 的时间点还原](point-in-time-restore-overview.md)
- [软删除](soft-delete-overview.md)
- [更改源](storage-blob-change-feed.md)
- [Blob 版本控制](versioning-overview.md)
