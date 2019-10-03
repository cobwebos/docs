---
title: 使用 Azure Data Box、Azure Data Box Heavy 将数据发送到热、冷、存档 Blob 层 | 数据中的 Microsoft Docs
description: 介绍如何使用 Azure Data Box 或 Azure Data Box Heavy 将数据发送到合适的块 Blob 存储层（如热、冷或存档存储层）
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: ea208c395e2ef69ce8f28052351643e963cceb05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427871"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>使用 Azure Data Box 或 Azure Data Box Heavy 将数据发送到合适的 Azure 存储 Blob 层

Azure Data Box 通过向你发送专有存储设备来将大量数据移动到 Azure。 使用数据填充设备并将其返回。 Data Box 中的数据会上传到和存储帐户有关的默认层级。 然后便可以将数据移到另一个存储层。

本文介绍可如何将 Data Box 上传的数据移动到热、冷或存档 Blob 层。 本文同时适用于 Azure Data Box 和 Azure Data Box Heavy。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>为数据选择适当的存储层

凭借 Azure 存储，可通过三个不同层级以最经济高效的方式存储数据，三个层级即热、冷或存档存储层。 热存储层为存储经常访问的数据进行了优化。 热存储的存储成本高于冷存储和存档存储，但访问成本最低。

冷存储层适用于需要存储至少 30 天的不经常访问的数据。 冷存储层成本低于热存储层，但数据访问费用较高。

Azure 存档存储层是脱机的，其存储成本最低，但访问成本最高。 此层适用于要在存档存储中保留至少 180 天的数据。 有关这些层级和定价模型的详细信息，请转到[存储层的比较](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。

Data Box 或 Data Box Heavy 中的数据会上传到与存储帐户关联的存储层。 创建存储帐户时，可以将访问层指定为“热”或“冷”。 根据工作负荷和成本的访问模式，可以将数据从默认层级移动到另一个存储层。

只能在 Blob 存储或常规用途 v2 (GPv2) 帐户中对对象存储数据分层。 常规用途 v1 (GPv1) 帐户不支持分层。 若要为数据选择适当的存储层，请查看 [Azure Blob 存储：高级、热、冷和存档存储层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)中详细介绍的注意事项。

## <a name="set-a-default-blob-tier"></a>设置默认 Blob 层

在 Azure 门户中创建存储帐户时已指定默认 Blob 层。 选择 GPv2 或 blob 存储作为存储类型后，就可以指定访问层属性。 默认情况下，选择热存储层。

如果在订购 Data Box 或 Data Box Heavy 时尝试创建新帐户，则无法指定层级。 创建帐户后，可以在门户中修改帐户以设置默认访问层。

或者使用指定的访问层属性先创建存储帐户。 在创建 Data Box 或 Data Box Heavy 订单时，请选择现有存储帐户。 有关如何在存储帐户创建过程中设置默认 Blob 层的详细信息，请转到[在 Azure 门户中创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal)。

## <a name="move-data-to-a-non-default-tier"></a>将数据移到非默认层

Data Box 设备中的数据上传到默认层后，可能需要将数据移到非默认层。 可通过两种方法将数据移动到非默认层。

- Azure Blob 存储生命周期管理 - 可以使用基于策略的方法自动将数据分层，或使其在生命周期结束时过期  。 有关详细信息，请转到[管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)。
- 脚本 - 可以通过 Azure PowerShell 使用脚本的方法来启用 Blob 级别分层  。 可以调用 `SetBlobTier` 操作在 Blob 上设置层。

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>使用 Azure PowerShell 设置 Blob 层

以下步骤介绍如何使用 Azure PowerShell 脚本将 Blob 层设置为存档存储层。

1. 打开已提升的 Windows PowerShell 会话。 请确保运行的是 PowerShell 5.0 或更高版本。 键入：

   `$PSVersionTable.PSVersion`     

2. 登录到 Azure PowerShell。 

   `Login-AzAccount`  

3. 定义用于存储帐户、访问密钥、容器和存储上下文的变量。

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. 获取容器中的所有 Blob。

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. 将容器中所有 Blob 的层级设置为存档存储层。

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    下面显示了示例输出：

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > 如果想要将数据引入到存档存储层，请将默认帐户层设置为热存储层。 如果默认层为冷存储层，同时数据要立即移动到存档存储层，则会对提前 30 天删除进行惩罚。

## <a name="next-steps"></a>后续步骤

-  了解如何[使用生命周期策略规则解决通用数据分层方案](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

