---
title: 启用和管理块 blob 的时间点还原（预览）
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
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118203"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>启用和管理块 blob 的时间点还原（预览）

可以使用时间点还原（预览版）将块 blob 还原到较早时间点的状态。 本文介绍如何使用 PowerShell 为存储帐户启用时间点还原。 它还演示了如何使用 PowerShell 执行还原操作。

有关详细信息以及如何注册预览版的信息，请参阅[块 blob （预览版）的时点还原](point-in-time-restore-overview.md)。

> [!CAUTION]
> 时间点还原仅支持对块 blob 还原操作。 无法还原容器上的操作。 如果从存储帐户中删除容器，方法是在时间点还原预览期间调用 "[删除容器](/rest/api/storageservices/delete-container)" 操作，则不能使用还原操作来还原该容器。 在预览期间，如果你可能想要还原某个容器，则请删除该容器，而不是删除它。

> [!IMPORTANT]
> 时间点还原预览版仅适用于非生产。 生产服务级别协议 (SLA) 当前不可用。

## <a name="install-the-preview-module"></a>安装预览模块

若要通过 PowerShell 配置 Azure 时间点还原，请先安装[1.14.1](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) PowerShell 模块的版本。 按照以下步骤安装预览模块：

1. 使用 "**设置**" 下的 "**应用 & 功能**" 设置，从 Windows 中卸载任何以前的 Azure PowerShell 安装。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安装 Az preview module：

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

有关安装 Azure PowerShell 的详细信息，请参阅[通过 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="enable-and-configure-point-in-time-restore"></a>启用和配置时间点还原

在启用和配置时间点还原之前，请启用其先决条件：软删除、更改源和 blob 版本控制。 有关启用上述每项功能的详细信息，请参阅以下文章：

- [为 blob 启用软删除](soft-delete-enable.md)
- [启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [启用和管理 blob 版本控制](versioning-enable.md)

若要使用 PowerShell 配置 Azure 时间点还原，请调用 AzStorageBlobRestorePolicy 命令。 以下示例启用软删除保留期，启用 "更改源"，然后启用时间点还原。 运行此示例之前，请使用 Azure 门户或 Azure 资源管理器模板来启用 blob 版本控制。

运行此示例时，请记住将尖括号中的值替换为自己的值：

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

若要启动还原操作，请调用 AzStorageBlobRange 命令，并将还原点指定为 UTC**日期时间**值。 您可以指定一个或多个字典范围的 blob 进行还原，或省略一个范围以还原存储帐户的所有容器中的所有 blob。 还原操作可能需要几分钟才能完成。

在指定要还原的 blob 范围时，请记住以下规则：

- 为开始范围和结束范围指定的容器模式必须至少包含三个字符。 用于分隔容器名称和 blob 名称的正斜杠（/）不计入该最小值。
- 只能为每个还原操作指定一个范围。
- 不支持通配符。 它们被视为标准字符。
- 可以 `$root` `$web` 通过在传递到还原操作的范围内显式指定 blob，来还原和容器中的 blob。 `$root` `$web` 只有显式指定了和容器，才会还原和容器。 无法还原其他系统容器。

### <a name="restore-all-containers-in-the-account"></a>还原帐户中的所有容器

若要还原存储帐户中的所有容器和 blob，请调用 AzStorageBlobRange 命令，忽略 `-BlobRestoreRange` 参数。 下面的示例将存储帐户中的容器还原到当前时间之前的12小时：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>还原单个块 blob 范围

若要还原某个范围的 blob，请调用 AzStorageBlobRange 命令，并为参数指定字典和 blob 名称的字典范围 `-BlobRestoreRange` 。 范围的开始值为 "包含"，范围的结尾为 "独占"。

例如，若要还原名为*sample 容器*的单个容器中的 blob，可以指定一个以*sample-container*开头的范围，并以*container1*结尾。 不需要在开始和结束范围中指定的容器存在。 因为范围的结尾是独占的，即使存储帐户包含名为*container1*的容器，也只会还原名为*sample*容器的容器：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

若要指定要还原的容器中的 blob 子集，请使用正斜杠（/）将容器名称与 blob 模式分隔开。 例如，以下范围在单个容器中选择名称以字母*d*到*f*开头的 blob：

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

接下来，为 AzStorageBlobRange 命令提供范围。 通过为参数提供 UTC**日期时间**值来指定还原点 `-TimeToRestore` 。 下面的示例将指定范围内的 blob 还原到当前时间前3天的状态：

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>还原多个块 blob 范围

若要还原块 blob 的多个范围，请为参数指定范围数组 `-BlobRestoreRange` 。 下面的示例将还原*container1*和*container4*的完整内容：

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>后续步骤

- [块 blob 的时间点还原（预览）](point-in-time-restore-overview.md)
- [软删除](soft-delete-overview.md)
- [更改源（预览）](storage-blob-change-feed.md)
- [Blob 版本控制（预览）](versioning-overview.md)
