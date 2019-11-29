---
title: 为 Blob 存储设置和管理永久性策略-Azure 存储
description: 了解如何使用适用于 Blob （对象）存储的 WORM （写入一次，读取多个）支持，以在指定的时间间隔内以不可擦除、不可修改的状态存储数据。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 79d7454722900eb1d9d6280e35313ef2f4a5cd54
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555673"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>为 Blob 存储设置和管理永久性策略

使用 Azure Blob 存储的不可变存储，用户可以在蠕虫（写入一次，读取多个）状态下存储关键业务数据对象。 此状态可以根据用户指定的时间间隔使数据保持不可擦除且不可修改的状态。 在保留间隔期间，可以创建和读取 blob，但不能修改或删除它们。 不可变存储可用于所有 Azure 区域中的常规用途 v2 和 Blob 存储帐户。

本文介绍如何使用 Azure 门户、PowerShell 或 Azure CLI 为 Blob 存储中的数据设置和管理永久性策略和合法保留。 有关不可变存储的详细信息，请参阅[将业务关键 blob 数据存储在不可变的存储](storage-blob-immutable-storage.md)中。

## <a name="set-retention-policies-and-legal-holds"></a>设置保留策略和合法保留

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. 请创建新的容器或选择现有容器，以便存储需要保持不可变状态的 Blob。 容器必须位于常规用途 v2 或 Blob 存储帐户中。

2. 在容器设置中选择“访问策略”。 然后选择 "**不可变 blob 存储**" 下的 "**添加策略**"。

    ![门户中的容器设置](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. 若要启用基于时间的保留，请从下拉菜单中选择“基于时间的保留”。

    ![在“策略类型”下选择“基于时间的保留”](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. 输入保留间隔（天）（可接受的值为1到146000天）。

    ![“将保留期更新为”框](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    此策略的初始状态为 "已解锁"，使你能够在锁定该功能之前对其进行测试并对其进行更改。 锁定策略对于符合 SEC 17a-4 等法规至关重要。

5. 锁定策略。 右键单击省略号（ **...** ），将显示以下菜单，其中包含其他操作：

    ![菜单中的“锁定策略”](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. 选择 "**锁定策略**" 并确认锁定。 策略现已锁定，无法删除，只允许使用保留间隔的扩展。 不允许 Blob 删除和重写。 

    ![确认菜单上的 "锁定策略"](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. 若要启用法律持有，请选择 "**添加策略**"。 从下拉菜单中选择“法定保留”。

    ![菜单中“策略类型”下面的“法定保留”](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. 使用一个或多个标记创建法定保留。

    ![策略类型下面的“标记名称”框](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. 若要清除合法保留，只需删除应用的合法保留标识符标记。

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

以下命令组包含该功能：`az storage container immutability-policy` 和 `az storage container legal-hold`。 对这些命令运行 `-h` 可查看命令。

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az 模块支持不可变的存储。  若要启用该功能，请执行以下步骤：

1. 确保已安装最新版本的 PowerShellGet：`Install-Module PowerShellGet –Repository PSGallery –Force`。
2. 删除以前安装的 Azure PowerShell。
3. 安装 Azure PowerShell：`Install-Module Az –Repository PSGallery –AllowClobber`。

以下示例 PowerShell 脚本仅供参考。 此脚本创建新的存储帐户和容器。 然后，它演示如何设置和清除法定保留、创建和锁定基于时间的保留策略（也称为不可变策略），并延长保留时间间隔。

首先，创建一个 Azure 存储帐户：

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

设置并清除法定保留：

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

创建或更新不可变策略：

```powershell
# Create an immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

检索不可变策略：

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

锁定永久性策略（添加 `-Force` 以消除提示）：

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

扩展不可变策略：

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

删除未锁定的永久性策略（添加 `-Force` 以消除提示）：

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="next-steps"></a>后续步骤

[将业务关键 blob 数据存储在不可变的存储中](storage-blob-immutable-storage.md)