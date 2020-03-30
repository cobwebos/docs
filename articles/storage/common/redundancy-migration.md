---
title: 更改存储帐户的复制方式
titleSuffix: Azure Storage
description: 了解如何更改现有存储帐户中的数据复制方式。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337073"
---
# <a name="change-how-a-storage-account-is-replicated"></a>更改存储帐户的复制方式

Azure 存储始终存储数据的多个副本，以便防止其免受计划内和计划外事件的影响，包括瞬时硬件故障、网络或断电以及大规模自然灾害。 冗余可确保存储帐户符合 Azure[存储的服务级别协议 （SLA），](https://azure.microsoft.com/support/legal/sla/storage/)即使在发生故障时也是如此。

Azure 存储提供以下类型的复制：

- 本地冗余存储 (LRS)
- 区域冗余存储 (ZRS)
- 异地冗余存储 （GRS） 或读取访问异地冗余存储 （RA-GRS）
- 地理区域冗余存储 （GZRS） 或读取访问地理区域冗余存储 （RA-GZRS）（预览）

有关每个选项的概述，请参阅 Azure[存储冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>在复制类型之间切换

您可以将存储帐户从一种类型的复制切换到任何其他类型，但某些方案比其他方案更直接。 如果要添加或删除对辅助区域的异地复制或读取访问，可以使用 Azure 门户、PowerShell 或 Azure CLI 更新复制设置。 但是，如果要更改在主区域中复制数据的方式，通过从 LRS 移动到 ZRS，反之亦然，则必须执行手动迁移。

下表概述了如何从每种复制类型切换到另一种类型的复制：

| 开关 | ...到 LRS | ...到 GRS/RA-GRS | ...到 ZRS | ...到 GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>...从 LRS</b> | 空值 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1</sup> | 执行手动迁移 <br /><br />请求实时迁移 | 执行手动迁移 <br /><br /> 或 <br /><br /> 先切换到 GRS/RA-GRS，然后请求实时迁移<sup>1</sup> |
| <b>...从 GRS/RA-GRS</b> | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 空值 | 执行手动迁移 <br /><br /> 或 <br /><br /> 先切换到 LRS，然后请求实时迁移 | 执行手动迁移 <br /><br /> 请求实时迁移 |
| <b>...从 ZRS</b> | 执行手动迁移 | 执行手动迁移 | 空值 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1</sup> |
| <b>...从 GZRS/RA-GZRS</b> | 执行手动迁移 | 执行手动迁移 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 空值 |

<sup>1</sup>产生一次性出口费用。

> [!CAUTION]
> 如果对 （RA-） GRS 或 （RA-） GZRS 帐户执行[帐户故障转移](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance)，则新主区域中将其配置为本地冗余。 不支持此类 LRS 帐户的实时迁移到 ZRS 或 GZRS。 您将需要执行[手动迁移](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)。

## <a name="change-the-replication-setting"></a>更改复制设置

只要未更改在主区域中复制数据的方式，可以使用 Azure 门户、PowerShell 或 Azure CLI 更改存储帐户的复制设置。 如果要从主区域中的 LRS 迁移到主区域中的 ZRS，反之亦然，则必须执行[手动迁移](#perform-a-manual-migration-to-zrs)或[实时迁移](#request-a-live-migration-to-zrs)。

更改存储帐户的复制方式不会导致应用程序的停机时间。

# <a name="portal"></a>[门户](#tab/portal)

要更改 Azure 门户中的存储帐户的冗余选项，请按照以下步骤操作：

1. 导航到 Azure 门户中的存储帐户。
1. 选择 **"配置"** 设置。
1. 更新**复制**设置。

![显示如何在门户中更改复制选项的屏幕截图](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要使用 PowerShell 更改存储帐户的冗余选项，请调用[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)命令并`-SkuName`指定参数：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 更改存储帐户的冗余选项，请调用[az 存储帐户更新](/cli/azure/storage/account#az-storage-account-update)命令`--sku`并指定参数：

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>执行手动迁移到 ZRS

如果要更改存储帐户中的数据在主区域中的复制方式，通过从 LRS 移动到 ZRS，反之亦然，则可以选择执行手动迁移。 手动迁移比实时迁移更灵活。 您可以控制手动迁移的时间，因此，如果您需要迁移以在特定日期完成，请使用此选项。

当您在主区域中执行从 LRS 到 ZRS 的手动迁移时，目标存储帐户可以是异地冗余的，也可以配置为对辅助区域的读取访问。 例如，您可以一步将 LRS 帐户迁移到 GZRS 或 RA-GZRS 帐户。

手动迁移可能导致应用程序关闭。 如果应用程序需要高可用性，则还可以使用 Microsoft 提供的实时迁移选项。 实时迁移属于就地迁移，不停机。

通过手动迁移，可以将数据从现有存储帐户复制到在主区域中使用 ZRS 的新存储帐户。 要执行手动迁移，可以使用以下选项之一：

- 使用现有工具（如 AzCopy、Azure 存储客户端库之一或可靠的第三方工具）复制数据。
- 如果您熟悉 Hadoop 或 HDInsight，则可以将源存储帐户和目标存储帐户附加到群集。 然后使用 DistCp 等工具来并行化数据复制过程。

## <a name="request-a-live-migration-to-zrs"></a>请求实时迁移到 ZRS

如果需要将存储帐户从 LRS 或 GRS 迁移到主区域中的 ZRS，而没有应用程序停机，则可以请求 Microsoft 进行实时迁移。 在实时迁移期间，您可以访问存储帐户中的数据，并且不会丢失持久性或可用性。 Azure 存储 SLA 在迁移过程中维护。 没有与实时迁移关联的数据丢失。 迁移后，服务终结点、访问密钥、共享访问签名和其他帐户选项保持不变。

ZRS 仅支持通用 v2 帐户，因此请确保在提交实时迁移到 ZRS 的请求之前升级存储帐户。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。 存储帐户必须包含要通过实时迁移迁移的数据。

只有使用 LRS 或 GRS 复制的存储帐户才支持实时迁移。 如果你的帐户使用 RA-GRS，则需要先将帐户的复制类型更改为 LRS 或 GRS，然后继续。 此中间步骤可确保在迁移之前，删除 RA-GRS 提供的辅助只读终结点。

尽管 Microsoft 会尽快处理实时迁移的请求，但无法保证实时迁移何时完成。 如果需要在特定的日期之前将数据迁移到 ZRS，则 Microsoft 建议执行手动迁移。 通常，帐户中的数据越多，迁移这些数据所需的时间就越长。

您必须执行手动迁移，如果：

- 您希望将数据迁移到位于与源帐户不同的区域中的 ZRS 存储帐户。
- 您的存储帐户是高级页面 blob 或块 Blob 帐户。
- 您希望将数据从 ZRS 迁移到 LRS、GRS 或 RA-GRS。
- 您的存储帐户包括存档层中的数据。

可以通过 [Azure 支持门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)请求实时迁移。 在门户中，选择要转换为 ZRS 的存储帐户。

1. 选择**新的支持请求**
2. 根据帐户信息填写“基本信息”。**** 在“服务”部分，选择“存储帐户管理”以及要转换为 ZRS 的资源。********
3. 选择“下一步”。
4. 在“问题”部分指定以下值：****
    - **严重性**：保留默认值。
    - **问题类型**：选择“数据迁移”。****
    - **类别**： 选择**迁移到 ZRS**。
    - **标题**：键入描述性的标题，例如“ZRS 帐户迁移”。****
    - **详细信息**：在 **"详细信息"** 框中键入其他详细信息，例如，我想从\_\_该区域的 [LRS， GRS] 迁移到 ZRS。
5. 选择“下一步”。
6. 检查“联系信息”边栏选项卡中的联系信息是否正确。****
7. 选择 **“创建”**。

支持人员将与你取得联系，并提供所需的任何帮助。

> [!NOTE]
> 高级文件共享当前不支持实时迁移。 目前仅支持手动复制或移动数据。
>
> GZRS 存储帐户当前不支持存档层。 有关详细信息[，请参阅 Azure Blob 存储：热、酷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
>
> 托管磁盘仅适用于 LRS，不能迁移到 ZRS。 您可以将标准 SSD 托管磁盘的快照和映像存储在标准硬盘存储上，并在[LRS 和 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。 有关与可用性集集成的信息，请参阅[Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。

## <a name="switch-from-zrs-classic"></a>从 ZRS 经典切换

> [!IMPORTANT]
> Microsoft 将于 2021 年 3 月 31 日弃用并迁移 ZRS 经典版帐户。 在弃用之前会向 ZRS 经典版客户提供更多详细信息。
>
> ZRS 在给定区域中普遍可用后，客户将无法再从该区域的 Azure 门户创建 ZRS 经典帐户。 在弃用 ZRS 经典版之前，可以使用 Microsoft PowerShell 和 Azure CLI 创建 ZRS 经典版帐户。 有关 ZRS 可用位置的信息，请参阅[Azure 存储冗余](storage-redundancy.md)。

ZRS 经典版以异步方式在一到两个区域中的数据中心之间复制数据。 除非 Microsoft 发起了到次要区域的故障转移，否则复制的数据可能不可用。 ZRS 经典版帐户无法与 LRS、GRS 或 RA-GRS 相互转换。 ZRS 经典版帐户也不支持指标或日志记录。

ZRS 经典版仅适用于常规用途 V1 (GPv1) 存储帐户中的**块 Blob**。 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](storage-account-overview.md)。

要手动将 ZRS 帐户数据迁移到 LRS、GRS、RA-GRS 或 ZRS 经典帐户，请使用以下工具之一：AzCopy、Azure 存储资源管理器、PowerShell 或 Azure CLI。 此外，可以使用某个 Azure 存储客户端库生成自己的迁移解决方案。

您还可以在 ZRS 可用的区域中使用 Azure 门户、PowerShell 或 Azure CLI 将 ZRS 经典存储帐户升级到 ZRS。

# <a name="portal"></a>[门户](#tab/portal)

要在 Azure 门户中升级到 ZRS，请导航到帐户的 **"配置"** 设置，然后选择 **"升级**" ：

![在门户中将 ZRS 经典升级到 ZRS](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[电源外壳](#tab/powershell)

要使用 PowerShell 升级到 ZRS，请调用以下命令：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 升级到 ZRS，请调用以下命令：

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>与更改数据复制方式相关的成本

与更改数据复制方式相关的成本取决于转化路径。 从最少到最昂贵的 Azure 存储冗余产品的顺序包括 LRS、ZRS、GRS、RA-GRS、GZRS 和 RA-GZRS。

例如，从 LRS 转移到任何其他类型的复制会产生额外的费用，因为这是转移到更高级的冗余级别。** 迁移到 GRS 或 RA-GRS 会产生出口带宽费用，因为（主要区域中的）数据将复制到远程次要区域。** 此费用是在初始设置期间收取的一次性费用。 复制数据后，无需进一步支付迁移费用。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果将存储帐户从 GRS 迁移到 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

> [!IMPORTANT]
> 如果将存储帐户从 RA-GRS 迁移到 GRS 或 LRS，则会在完成转换 30 天后以 RA-GRS 方式对该帐户计费。

## <a name="see-also"></a>请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [检查存储帐户的"上次同步时间"属性](last-sync-time-get.md)
- [使用读取访问异地冗余存储设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)
