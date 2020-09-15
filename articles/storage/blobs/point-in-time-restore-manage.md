---
title: 为块 blob 启用和管理时间点还原（预览版）
titleSuffix: Azure Storage
description: 了解如何使用时间点还原 (预览) 将一组块 blob 还原为以前的状态。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068467"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>为块 blob 启用和管理时间点还原（预览版）

可以使用时间点还原 (预览) 将一组块 blob 还原为以前的状态。 本文介绍了如何使用 PowerShell 为存储帐户启用时间点还原。 它还展示了如何使用 PowerShell 执行还原操作。

有关详细信息以及如何注册预览版的信息，请参阅[块 blob 的时间点还原（预览版）](point-in-time-restore-overview.md)。

> [!CAUTION]
> 时间点还原仅支持对块 blob 执行还原操作。 无法还原对容器的操作。 在时间点还原预览版期间，如果你通过调用[删除容器](/rest/api/storageservices/delete-container)操作从存储帐户中删除了容器，将无法使用还原操作来还原该容器。 在预览版期间，如果希望能够还原，请不要删除容器，而是删除各个 blob。

> [!IMPORTANT]
> 此时间点还原预览版仅适用于非生产用途。

## <a name="enable-and-configure-point-in-time-restore"></a>启用和配置时间点还原

在启用和配置时间点还原之前，请为存储帐户启用其先决条件：软删除、更改源和 blob 版本控制。 若要详细了解如何启用上述每项功能，请参阅以下文章：

- [为 blob 启用软删除](soft-delete-enable.md)
- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

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

若要使用 PowerShell 配置时间点还原，请首先安装 Az. Storage preview module version 1.14.1-preview 或更高版本的预览版模块。 删除其他版本的 Az.Storage 模块。

验证是否已安装 PowerShellGet 版本2.2.4.1 或更高版本。 若要确定当前安装的版本，请运行以下命令：

```powershell
Get-InstalledModule PowerShellGet
```

接下来，安装 Az preview 模块。 以下命令将安装 Az [2.5.2](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) 模块的版本：

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

有关安装 Azure PowerShell 的详细信息，请参阅 [安装 powershellget](/powershell/scripting/gallery/installing-psget) 并 [通过 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

若要使用 PowerShell 配置 Azure 时间点还原，请调用 Enable-AzStorageBlobRestorePolicy 命令。 以下示例启用软删除并设置软删除保留期，启用更改源，然后启用时间点还原。 运行此示例之前，还需要使用 Azure 门户或 Azure 资源管理器模板来启用 blob 版本控制。

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

仅还原块 blob。 还原操作中不包含页 blob 和追加 blob。 有关与追加 blob 相关的限制的详细信息，请参阅 [已知问题](#known-issues)。

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

若要通过 PowerShell 还原存储帐户中的所有容器和 blob，请调用 **AzStorageBlobRange** 命令，忽略 `-BlobRestoreRange` 参数。 下面的示例将存储帐户中的容器还原到当前时间后推 12 小时的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
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

若要还原单个范围的 blob，请调用 **AzStorageBlobRange** 命令，并为参数指定字典和 blob 名称的字典范围 `-BlobRestoreRange` 。 例如，若要还原名为 sample-container 的单个容器中的 blob，可以指定以 *sample-container* 开头且以 *sample-container1* 结尾的范围。 不要求在开始和结束范围中指定的容器存在。 因为不包括范围的结束值，所以即使存储帐户包含名为 *sample-container1* 的容器，也只会还原名为 *sample-container* 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

若要指定要还原的容器中的 blob 子集，请使用正斜杠 (/) 将容器名称与 blob 前缀模式分隔开。 例如，下面的范围选择单个容器中名称以字母 *d* 到 *f* 开头的 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

接下来，为 **AzStorageBlobRange** 命令提供范围。 通过为 `-TimeToRestore` 参数提供一个 UTC **日期/时间**值来指定还原点。 下面的示例将指定范围内的 blob 还原到当前时间后推 3 天的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

若要还原多个范围的块 blob，请为 `-BlobRestoreRange` 参数指定一个范围数组。 下面的示例指定两个范围来还原 *container1* 和 *container4*的完整内容：

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>通过 PowerShell 异步还原块 blob

若要异步运行还原操作，请将 `-AsJob` 参数添加到对 **AzStorageBlobRange** 的调用，并将调用结果存储在变量中。 **AzStorageBlobRange**命令返回**AzureLongRunningJob**类型的对象。 您可以检查此对象的 **State** 属性以确定还原操作是否已完成。 **State**属性的值可以是 "**正在运行**" 或 "**已完成**"。

下面的示例演示如何以异步方式调用还原操作：

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

若要在还原操作运行之后等待完成，请调用 [wait](/powershell/module/microsoft.powershell.core/wait-job) 命令，如以下示例中所示：

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>已知问题

对于存在追加 blob 的还原操作子集，还原操作将失败。 如果帐户中存在追加 blob，Microsoft 建议你在预览期间不要执行时间点还原。

## <a name="next-steps"></a>后续步骤

- [块 blob 的时间点还原（预览版）](point-in-time-restore-overview.md)
- [软删除](soft-delete-overview.md)
- [更改源（预览版）](storage-blob-change-feed.md)
- [Blob 版本控制](versioning-overview.md)
