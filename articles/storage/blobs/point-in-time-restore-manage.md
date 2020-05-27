---
title: 为块 blob 启用和管理时间点还原（预览版）
titleSuffix: Azure Storage
description: 了解如何使用时间点还原（预览版）将块 blob 还原到较早时间点的状态。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118203"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>为块 blob 启用和管理时间点还原（预览版）

可以使用时间点还原（预览版）将块 blob 还原到较早时间点的状态。 本文介绍了如何使用 PowerShell 为存储帐户启用时间点还原。 它还展示了如何使用 PowerShell 执行还原操作。

有关详细信息以及如何注册预览版的信息，请参阅[块 blob 的时间点还原（预览版）](point-in-time-restore-overview.md)。

> [!CAUTION]
> 时间点还原仅支持对块 blob 执行还原操作。 无法还原对容器的操作。 在时间点还原预览版期间，如果你通过调用[删除容器](/rest/api/storageservices/delete-container)操作从存储帐户中删除了容器，将无法使用还原操作来还原该容器。 在预览版期间，如果希望能够还原，请不要删除容器，而是删除各个 blob。

> [!IMPORTANT]
> 此时间点还原预览版仅适用于非生产用途。 生产服务级别协议 (SLA) 当前不可用。

## <a name="install-the-preview-module"></a>安装预览版模块

若要通过 PowerShell 配置 Azure 时间点还原，请先安装 Az.Storage PowerShell 模块的 [1.14.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) 版本。 若要安装预览版模块，请按照以下步骤操作：

1. 使用“设置”下的“应用和功能”设置从 Windows 中卸载以前安装的所有 Azure PowerShell。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安装 Az.Storage 预览版模块：

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

有关如何安装 Azure PowerShell 的详细信息，请参阅[使用 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="enable-and-configure-point-in-time-restore"></a>启用和配置时间点还原

在启用和配置时间点还原之前，请启用其先决条件：软删除、更改源和 blob 版本控制。 若要详细了解如何启用上述每项功能，请参阅以下文章：

- [为 blob 启用软删除](soft-delete-enable.md)
- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

若要使用 PowerShell 配置 Azure 时间点还原，请调用 Enable-AzStorageBlobRestorePolicy 命令。 以下示例启用软删除并设置软删除保留期，启用更改源，然后启用时间点还原。 运行此示例之前，还需要使用 Azure 门户或 Azure 资源管理器模板来启用 blob 版本控制。

运行此示例时，请务必将尖括号中的值替换为你自己的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>执行还原操作

若要启动还原操作，请调用 Restore-AzStorageBlobRange 命令，并将还原点指定为某个 UTC **日期/时间**值。 可以指定一个或多个字典范围的 blob 进行还原，或省略范围以还原存储帐户的所有容器中的所有 blob。 还原操作可能需要几分钟才能完成。

在指定要还原的 blob 范围时，请记住以下规则：

- 为开始范围和结束范围指定的容器模式必须包含至少三个字符（即最小值为 3）。 用于分隔容器名称与 blob 名称的正斜杠 (/) 不计入该最小值。
- 只能为每个还原操作指定一个范围。
- 不支持通配符。 它们被视为标准字符。
- 可以还原 `$root` 和 `$web` 容器中的 blob，方法是：在传递给还原操作的范围中显式指定它们。 `$root` 和 `$web` 容器只有在显式指定的情况下才会进行还原。 无法还原其他系统容器。

### <a name="restore-all-containers-in-the-account"></a>还原帐户中的所有容器

若要还原存储帐户中的所有容器和 blob，请调用 Restore-AzStorageBlobRange 命令并省略 `-BlobRestoreRange` 参数。 下面的示例将存储帐户中的容器还原到当前时间后推 12 小时的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>还原单个块 blob 范围

若要还原某个范围的 blob，请调用 Restore-AzStorageBlobRange 命令并为 `-BlobRestoreRange` 参数指定容器和 blob 名称的字典范围。 包括范围的开始值，但不包括范围的结束值。

例如，若要还原名为 sample-container 的单个容器中的 blob，可以指定以 *sample-container* 开头且以 *sample-container1* 结尾的范围。 不要求在开始和结束范围中指定的容器存在。 因为不包括范围的结束值，所以即使存储帐户包含名为 *sample-container1* 的容器，也只会还原名为 *sample-container* 的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

若要指定容器中要还原的 blob 子集，请使用正斜杠 (/) 将容器名称与 blob 模式分隔开。 例如，下面的范围选择单个容器中名称以字母 *d* 到 *f* 开头的 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

接下来，为 Restore-AzStorageBlobRange 命令提供范围。 通过为 `-TimeToRestore` 参数提供一个 UTC **日期/时间**值来指定还原点。 下面的示例将指定范围内的 blob 还原到当前时间后推 3 天的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>还原多个块 blob 范围

若要还原多个范围的块 blob，请为 `-BlobRestoreRange` 参数指定一个范围数组。 下面的示例还原 *container1* 和 *container4* 的完整内容：

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>后续步骤

- [块 blob 的时间点还原（预览版）](point-in-time-restore-overview.md)
- [软删除](soft-delete-overview.md)
- [更改源（预览版）](storage-blob-change-feed.md)
- [Blob 版本控制（预览版）](versioning-overview.md)
