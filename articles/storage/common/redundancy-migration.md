---
title: 更改存储帐户的复制方式
titleSuffix: Azure Storage
description: 了解如何更改现有存储帐户中数据的复制方式。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5d047aa3c5c937e3b84b8fa672101bc801221067
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871363"
---
# <a name="change-how-a-storage-account-is-replicated"></a>更改存储帐户的复制方式

Azure 存储始终会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 即使面临故障时，冗余也可确保存储帐户满足 [Azure 存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) 的要求。

Azure 存储提供以下类型的复制：

- 本地冗余存储 (LRS)
- 区域冗余存储 (ZRS)
- 异地冗余存储（GRS）或读取访问异地冗余存储（RA-GRS）
- 区域冗余存储（GZRS）或读取访问权限异地冗余存储（RA-GZRS）

有关这些选项的概述，请参阅[Azure 存储冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>在复制类型之间切换

可以将存储帐户从一种类型的复制切换到任何其他类型，但有些方案比其他方案更直接。 若要添加或删除对次要区域的异地复制或读取访问权限，可以使用 Azure 门户、PowerShell 或 Azure CLI 来更新复制设置。 但是，如果想要更改在主要区域中复制数据的方式，请通过从 LRS 转到 ZRS，反之亦然。

下表概述了如何从各种类型的复制切换到另一种类型的复制：

| 更换 | ...到 LRS | ...to GRS/RA-GRS | ...到 ZRS | ...to GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>...from LRS</b> | 不可用 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1</sup> | 执行手动迁移 <br /><br />请求实时迁移 | 执行手动迁移 <br /><br /> 或 <br /><br /> 先切换到 GRS/RA-GRS，然后请求实时迁移<sup>1</sup> |
| <b>...from GRS/RA-GRS</b> | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 不可用 | 执行手动迁移 <br /><br /> 或 <br /><br /> 先切换到 LRS，然后请求实时迁移 | 执行手动迁移 <br /><br /> 请求实时迁移 |
| <b>...from ZRS</b> | 执行手动迁移 | 执行手动迁移 | 不可用 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1、2</sup> |
| <b>...from GZRS/RA-GZRS</b> | 执行手动迁移 | 执行手动迁移 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 不可用 |

<sup>1</sup>会产生一次性的出口费用。<br />
<sup>2</sup>在以下区域中不支持从 ZRS 到 GZRS/RA-GZRS，反之亦然：，反之亦然。

> [!CAUTION]
> 如果对（RA-） GRS 或（RA-） GZRS 帐户执行[帐户故障转移](storage-disaster-recovery-guidance.md)，则在故障转移后，该帐户在新的主要区域中为本地冗余。 不支持通过故障转移导致的 LRS 帐户的实时迁移到 ZRS 或 GZRS。 你将需要执行[手动迁移](#perform-a-manual-migration-to-zrs)到 ZRS 或 GZRS。

## <a name="change-the-replication-setting"></a>更改复制设置

您可以使用 Azure 门户、PowerShell 或 Azure CLI 更改存储帐户的复制设置，只要不更改在主要区域中复制数据的方式。 如果要从主要区域中的 LRS 迁移到主要区域中的 ZRS，则必须执行[手动迁移](#perform-a-manual-migration-to-zrs)或[实时迁移](#request-a-live-migration-to-zrs)。

更改存储帐户的复制方式不会导致应用程序停机。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中更改存储帐户的冗余选项，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 选择**配置**设置。
1. 更新**复制**设置。

![演示如何在门户中更改复制选项的屏幕截图](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 更改存储帐户的冗余选项，请调用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)命令并指定`-SkuName`参数：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 更改存储帐户的冗余选项，请调用[az storage account update](/cli/azure/storage/account#az-storage-account-update)命令并指定`--sku`参数：

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>执行手动迁移到 ZRS

如果要更改存储帐户中的数据在主要区域中的复制方式，可通过从 LRS 移动到 ZRS，反之亦然。 手动迁移比实时迁移更灵活。 可以控制手动迁移的时间，因此，如果需要在特定日期完成迁移，请使用此选项。

在主要区域中执行从 LRS 到 ZRS 的手动迁移时，或反之亦然，目标存储帐户可以是异地冗余的，也可以配置为读取辅助区域的读取权限。 例如，你可以在一个步骤中将 LRS 帐户迁移到 GZRS 或 GZRS 帐户。

手动迁移可能导致应用程序关闭。 如果应用程序需要高可用性，则还可以使用 Microsoft 提供的实时迁移选项。 实时迁移属于就地迁移，不停机。

通过手动迁移，你可以将现有存储帐户中的数据复制到在主要区域使用 ZRS 的新存储帐户。 若要执行手动迁移，可以使用以下选项之一：

- 使用现有工具（如 AzCopy、Azure 存储客户端库之一或可靠的第三方工具）复制数据。
- 如果你熟悉 Hadoop 或 HDInsight，则可以将源存储帐户和目标存储帐户帐户附加到群集。 然后使用 DistCp 等工具来并行化数据复制过程。

## <a name="request-a-live-migration-to-zrs"></a>请求实时迁移到 ZRS

如果需要在不应用程序停机的情况下将存储帐户从 LRS 或 GRS 迁移到主区域中的 ZRS，则可以从 Microsoft 请求实时迁移。 在实时迁移期间，你可以访问你的存储帐户中的数据，并且不会损失持久性或可用性。 Azure 存储 SLA 在迁移过程中保留。 实时迁移不会造成数据丢失。 迁移后，服务终结点、访问密钥、共享访问签名和其他帐户选项保持不变。

ZRS 仅支持常规用途 v2 帐户，因此请确保在将实时迁移请求提交到 ZRS 之前升级存储帐户。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。 存储帐户必须包含要通过实时迁移来迁移的数据。

只有使用 LRS 或 GRS 复制的存储帐户才支持实时迁移。 如果你的帐户使用 RA-GRS，则需要先将帐户的复制类型更改为 LRS 或 GRS，然后继续。 此中间步骤可确保在迁移之前，删除 RA-GRS 提供的辅助只读终结点。

尽管 Microsoft 会尽快处理实时迁移的请求，但无法保证实时迁移何时完成。 如果需要在特定的日期之前将数据迁移到 ZRS，则 Microsoft 建议执行手动迁移。 通常，帐户中的数据越多，迁移这些数据所需的时间就越长。

如果需要，你必须执行手动迁移：

- 想要将数据迁移到 ZRS 存储帐户，该帐户所在的区域不同于源帐户。
- 存储帐户是一个高级页 blob 或块 blob 帐户。
- 需要将数据从 ZRS 迁移到 LRS、GRS 或 GRS。
- 存储帐户包含存档层中的数据。

可以通过 [Azure 支持门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)请求实时迁移。 在门户中，选择要转换为 ZRS 的存储帐户。

1. 选择**新的支持请求**
2. 根据帐户信息填写“基本信息”。**** 在“服务”部分，选择“存储帐户管理”以及要转换为 ZRS 的资源。********
3. 选择“**下一步**”。
4. 在“问题”部分指定以下值：****
    - **严重性**：保留默认值。
    - **问题类型**：选择“数据迁移”。****
    - **类别**：选择**迁移到 ZRS**。
    - **标题**：键入描述性的标题，例如“ZRS 帐户迁移”。****
    - **详细信息**：在 "**详细**信息" 框中键入其他详细信息，例如，我想要从\_ \_区域中的 [LRS，GRS] 迁移到 ZRS。
5. 选择“**下一步**”。
6. 检查“联系信息”边栏选项卡中的联系信息是否正确。****
7. 选择“创建”。 

支持人员将与你取得联系，并提供所需的任何帮助。

> [!NOTE]
> 高级文件共享目前不支持实时迁移。 目前仅支持手动复制或移动数据。
>
> GZRS 存储帐户当前不支持存档层。 有关更多详细信息，请参阅[Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
>
> 托管磁盘仅适用于 LRS，无法迁移到 ZRS。 可以在标准 HDD 存储上存储标准 SSD 托管磁盘的快照和映像，并在[LRS 和 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。 有关与可用性集的集成的详细信息，请参阅[Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。

## <a name="switch-from-zrs-classic"></a>从 ZRS 经典切换

> [!IMPORTANT]
> Microsoft 将于 2021 年 3 月 31 日弃用并迁移 ZRS 经典版帐户。 在弃用之前会向 ZRS 经典版客户提供更多详细信息。
>
> 在给定区域中 ZRS 正式发布后，客户将无法再从该区域中的 Azure 门户创建 ZRS 经典帐户。 在弃用 ZRS 经典版之前，可以使用 Microsoft PowerShell 和 Azure CLI 创建 ZRS 经典版帐户。 有关 ZRS 的可用位置的信息，请参阅[Azure 存储冗余](storage-redundancy.md)。

ZRS 经典版以异步方式在一到两个区域中的数据中心之间复制数据。 除非 Microsoft 发起了到次要区域的故障转移，否则复制的数据可能不可用。 ZRS 经典版帐户无法与 LRS、GRS 或 RA-GRS 相互转换。 ZRS 经典版帐户也不支持指标或日志记录。

ZRS 经典版仅适用于常规用途 V1 (GPv1) 存储帐户中的**块 Blob**。 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](storage-account-overview.md)。

若要手动将 ZRS 帐户数据迁移到 LRS、GRS、GRS 或 ZRS 经典帐户，请使用以下工具之一： AzCopy、Azure 存储资源管理器、PowerShell 或 Azure CLI。 此外，可以使用某个 Azure 存储客户端库生成自己的迁移解决方案。

你还可以使用 ZRS 提供的区域中的 Azure 门户、PowerShell 或 Azure CLI 将 ZRS 经典存储帐户升级到 ZRS。

# <a name="portal"></a>[Portal](#tab/portal)

若要升级到 Azure 门户中的 ZRS，请导航到该帐户的**配置**设置，然后选择 "**升级**"：

![将 ZRS 经典升级到门户中的 ZRS](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 升级到 ZRS，请调用以下命令：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 升级到 ZRS，请调用以下命令：

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>与更改复制数据的方式相关的成本

与更改复制数据的方式相关的成本取决于您的转换路径。 从最高到最昂贵的 Azure 存储冗余产品的顺序，包括 LRS、ZRS、GRS、RA-GRS、GZRS 和 RA-GZRS。

例如，从 LRS 转移到任何其他类型的复制会产生额外的费用，因为这是转移到更高级的冗余级别。** 迁移到 GRS 或 RA-GRS 会产生出口带宽费用，因为（主要区域中的）数据将复制到远程次要区域。** 此费用是在初始设置期间收取的一次性费用。 复制数据后，无需进一步支付迁移费用。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果将存储帐户从 GRS 迁移到 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

> [!IMPORTANT]
> 如果将存储帐户从 RA-GRS 迁移到 GRS 或 LRS，则会在完成转换 30 天后以 RA-GRS 方式对该帐户计费。

## <a name="see-also"></a>另请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [检查存储帐户的 "上次同步时间" 属性](last-sync-time-get.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
