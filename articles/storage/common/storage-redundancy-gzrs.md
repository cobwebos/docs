---
title: 使用地理区域冗余存储（GZRS）构建高度可用的 Azure 存储应用程序（预览版） |Microsoft Docs
description: 异地冗余存储（GZRS）结婚时会区域冗余存储（ZRS）的高可用性，并可防止异地冗余存储（GRS）提供的区域性中断。 GZRS 存储帐户中的数据将在主要区域中的 Azure 可用性区域之间进行复制，并且还会复制到辅助地理区域，以防止区域灾难。
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c102941920d2b8746a49be47ef4c5613fa0bc281
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719069"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>使用地理区域冗余存储（GZRS）构建高度可用的 Azure 存储应用程序（预览版）

区域冗余存储（GZRS）（预览版）结婚时会[区域冗余存储（ZRS）](storage-redundancy-zrs.md)的高可用性，通过[异地冗余存储（GRS）](storage-redundancy-grs.md)提供的区域中断保护。 将 GZRS 存储帐户中的数据复制到主要区域中的三个[Azure 可用性区域](../../availability-zones/az-overview.md)，并将其复制到辅助地理区域，以防止区域灾难。 각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 有关更多详细信息和异常，请参阅[文档](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

使用 GZRS 存储帐户，可以在可用性区域变得不可用或无法恢复时继续读取和写入数据。 此外，在发生整个区域中断或发生主要区域无法恢复的灾难时，数据也是持久的。 GZRS 设计为在给定的一年内提供至少99.99999999999999% （16个9）的对象持久性。 GZRS 还提供与 LRS、ZRS、GRS 或 RA-GRS 相同的可伸缩性目标。 如果你的应用程序需要能够在主要区域发生灾难时读取数据，则可以选择启用对辅助区域中的数据的读取访问（使用读取访问权限异地冗余存储（GZRS））。

Microsoft 建议为需要一致性、持续性、高可用性、优秀性能和恢复能力的应用程序使用 GZRS。 对于发生区域性灾难时对辅助区域的 "读取" 访问权限的其他安全，请为你的存储帐户启用 GZRS。

## <a name="about-the-preview"></a>미리 보기 정보

仅常规用途 v2 存储帐户支持 GZRS 和 GZRS。 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요. GZRS 和 GZRS 支持块 blob、页 blob （不是 VHD 磁盘）、文件、表和队列。

GZRS 和 RA-GZRS 目前可用于以下区域：

- 동남아시아
- 북유럽
- 서유럽
- 일본 동부
- 영국 남부
- 미국 동부
- 미국 동부 2
- 미국 중부

Microsoft 继续在其他 Azure 区域启用 GZRS 和 GZRS。 请定期查看[Azure 服务更新](https://azure.microsoft.com/updates/)页，以了解有关支持的区域的信息。

有关预览版定价的信息，请参阅 GZRS preview 定价 for [blob](https://azure.microsoft.com/pricing/details/storage/blobs)、[文件](https://azure.microsoft.com/pricing/details/storage/files/)、[队列](https://azure.microsoft.com/pricing/details/storage/queues/)和[表](https://azure.microsoft.com/pricing/details/storage/tables/)。

> [!IMPORTANT]
> Microsoft 建议不要对生产工作负荷使用预览功能。

## <a name="how-gzrs-and-ra-gzrs-work"></a>GZRS 和 RA-GZRS 的工作方式

当数据写入到启用了 GZRS 或 GZRS 的存储帐户时，将首先在主区域跨三个可用性区域同步复制数据。 然后，将数据异步复制到数百英里之外的第二个区域。 向次要区域写入数据时，它会使用[本地冗余存储（LRS）](storage-redundancy-lrs.md)在该区域中以同步方式同步复制三次。

> [!IMPORTANT]
> 异步复制涉及到将数据写入主要区域和将数据复制到次要区域之间的延迟时间。 지역 재해의 경우 주 지역에서 해당 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.

创建存储帐户时，可以指定如何复制该帐户中的数据，还可以指定该帐户的主要区域。 异地复制帐户的成对次要区域是根据主要区域确定的，不能更改。 Azure에서 지원하는 지역에 대한 최신 정보는 [BCDR(비즈니스 연속성 및 재해 복구): Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요. 有关使用 GZRS 或 GZRS 创建存储帐户的信息，请参阅[创建存储帐户](storage-account-create.md)。

### <a name="use-ra-gzrs-for-high-availability"></a>使用 GZRS 实现高可用性

如果为存储帐户启用了 GZRS，则可以从辅助终结点以及存储帐户的主终结点读取数据。 辅助终结点将后缀 *–辅助*点追加到帐户名称。 예를 들어, Blob service에 대한 기본 엔드포인트가 `myaccount.blob.core.windows.net`인 경우, 보조 엔드포인트는 `myaccount-secondary.blob.core.windows.net`입니다. 스토리지 계정에 대한 액세스 키는 기본 및 보조 엔드포인트에 대해 동일합니다.

若要在发生区域性服务中断时充分利用 GZRS，必须事先设计应用程序来处理这种情况。 您的应用程序应在主终结点上进行读取和写入，但当主要区域不可用时，将切换到使用辅助终结点。 有关使用 GZRS 进行高可用性设计的指南，请参阅[使用 GZRS 或 GRS 设计高度可用的应用程序](https://docs.microsoft.com/azure/storage/common/storage-designing-ha-apps-with-ragrs)。

由于数据是以异步方式复制到辅助区域，因此次要区域通常位于主要区域后面。 若要确定哪些写入操作已复制到次要区域，应用程序需要检查存储帐户的上次同步时间。 在上次同步时间之前写入主要区域的所有写入操作都已成功复制到次要区域，这意味着它们可以从辅助区域读取。 在上次同步时间之后写入主要区域的任何写入操作都可能或尚未复制到次要区域，这意味着它们可能不可用于读取操作。

你可以使用 Azure PowerShell、Azure CLI 或 Azure 存储客户端库之一查询 "**上次同步时间**" 属性的值。 "**上次同步时间**" 属性是 GMT 日期/时间值。

有关 GZRS 的性能和可伸缩性的其他指导，请参阅[Microsoft Azure 存储性能和可伸缩性清单](storage-performance-checklist.md)。

### <a name="availability-zone-outages"></a>可用性区域中断

如果发生影响主要区域中的可用性区域的故障，应用程序可以使用该区域的其他可用性区域无缝地继续读取和写入存储帐户。 Microsoft 建议你在使用 GZRS 或 ZRS 时继续遵循暂时性故障处理的做法。 이러한 방식에는 지수적 백오프를 사용하여 재시도 정책을 구현하는 것이 포함됩니다.

当某个可用性区域变得不可用时，Azure undertakes 网络更新（如 DNS 重定位）。 如果在更新完成之前访问数据，这些更新可能会影响你的应用程序。

### <a name="regional-outages"></a>지역 중단

如果故障影响整个主要区域，Microsoft 将首先尝试还原主要区域。 如果无法进行还原，Microsoft 将故障转移到次要区域，以便次要区域成为新的主要区域。 如果存储帐户启用了 GZRS，则为此方案设计的应用程序可在等待故障转移时从次要区域读取。 如果存储帐户未启用 GZRS，则在故障转移完成之前，应用程序将无法从辅助数据库读取。

> [!NOTE]
> GZRS 和 RA-GZRS 目前仅在美国东部地区处于预览阶段。 美国东部2尚不提供客户管理的帐户故障转移（预览版），因此，客户当前无法使用 GZRS 和 RA GZRS 帐户来管理帐户故障转移事件。 在预览期间，Microsoft 将管理影响 GZRS 和 GZRS 帐户的任何故障转移事件。

由于数据是以异步方式复制到辅助区域的，因此如果无法恢复主要区域，则影响主要区域的故障可能会导致数据丢失。 最近写入主要区域和最后一次写入次要区域之间的时间间隔称为恢复点目标（RPO）。 RPO는 데이터를 복구할 수 있는 시점을 나타냅니다. Azure 存储的 RPO 通常小于15分钟，但目前没有 SLA 向次要区域复制数据所需的时间。

恢复时间目标（RTO）是衡量执行故障转移并使存储帐户恢复联机所花费的时间。 此度量值指示 Azure 通过将主要 DNS 条目更改为指向辅助位置来执行故障转移所需的时间。

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>将存储帐户迁移到 GZRS 或 GZRS

可以将任何现有存储帐户迁移到 GZRS 或 GZRS。 从现有的 ZRS 帐户迁移到 GZRS 或 RA-GZRS 非常简单，同时从 LRS、GRS 或 RA-GRS 帐户进行迁移更为简单。 以下各节介绍了如何在这两种情况下进行迁移。

**已知限制**

- （RA-） GZRS 帐户当前不支持存档层。 有关更多详细信息，请参阅[Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
- 托管磁盘不支持（RA-） GZRS。 可以将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并在[LRS 和 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="migrating-from-a-zrs-account"></a>从 ZRS 帐户迁移

若要将现有 ZRS 帐户转换为 GZRS，请使用[AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) cmdlet 更改帐户的 SKU。 请记住将占位符值替换为自己的值：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>从 LRS、GRS 或 GRS 帐户迁移

有两个选项可用于从 LRS、GRS 或 GRS 帐户迁移到 GZRS 或 GZRS：

- 可以手动将数据复制或移动到现有帐户中的新 GZRS 或 GZRS 帐户。
- 可以请求实时迁移。

#### <a name="perform-a-manual-migration"></a>执行手动迁移

如果需要在特定日期完成迁移，请考虑执行手动迁移。 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션을 사용하면 타이밍을 제어할 수 있습니다.

若要将数据从现有帐户手动迁移到 GZRS 或 GZRS 帐户，请使用可以有效地复制数据的工具。 일부 사례:

- 使用实用程序，如 AzCopy 或可靠的第三方工具。 有关 AzCopy 的信息，请参阅[AzCopy 入门](storage-use-azcopy-v10.md)。
- 如果你熟悉 Hadoop 或 HDInsight，请将源和目标存储帐户附加到群集。 接下来，使用 DistCp 等工具并行处理数据复制过程。
- Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 도구를 빌드합니다.

#### <a name="perform-a-live-migration"></a>执行实时迁移

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다.

在实时迁移期间，你可以在源存储帐户和目标存储帐户之间迁移数据时使用存储帐户。 在实时迁移过程中，你的帐户将继续满足其持久性和可用性的 SLA。 实时迁移不会造成停机或数据丢失。

只有常规用途 v2 帐户支持 GZRS/GZRS，因此，在将请求提交到 GZRS/RA-GZRS 之前，你必须将你的帐户升级到常规用途 v2。 有关详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)和[升级到常规用途 v2 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)。

迁移完成后，存储帐户的复制设置将更新为**异地区域冗余存储（GZRS）** 或**读取访问区域冗余存储（GZRS）** 。 服务终结点、访问密钥、共享访问签名（SAS）和任何其他帐户配置选项保持不变并保持不变。

실시간 마이그레이션에 대한 다음과 같은 제한에 유의하십시오.

- Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 如果需要将数据迁移到 GZRS 或 GZRS 的特定日期，Microsoft 建议改为执行手动迁移。 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다.
- 只有使用 GRS 或 GRS 复制的存储帐户才支持实时迁移。 如果你的帐户使用 LRS，则需要先将帐户的复制类型更改为 GRS 或 GRS，然后再继续。 此中间步骤添加 GRS/GRS 提供的辅助终结点。
- 계정에 데이터가 있어야 합니다.
- 동일한 지역 내에서만 데이터를 마이그레이션할 수 있습니다.
- 표준 스토리지 계정 유형만 실시간 마이그레이션을 지원합니다. 프리미엄 스토리지 계정은 수동으로 마이그레이션해야 합니다.
- 不支持从 GZRS 或 GZRS 帐户实时迁移到 LRS、GRS 或 RA-GRS 帐户。 데이터를 수동으로 새로운 스토리지 계정 또는 기존 스토리지 계정으로 이동해야 합니다.
- 可以请求从 GRS 到 GZRS 的实时迁移。 但是，不支持从 GRS 迁移到 GZRS。 在这种情况下，你必须请求将实时迁移到 GZRS，然后手动转换存储帐户以使用 GZRS。
- 托管磁盘仅支持 LRS，不能迁移到 GZRS 或 GZRS。 有关与可用性集的集成，请参阅[Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。
- 可以将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并在[LRS、ZRS、GZRS 和 RA-GZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。
- GZRS 不支持包含大型文件共享的帐户。

若要请求实时迁移，请使用[Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 从门户中，选择要迁移到 GZRS 或 GZRS 的存储帐户，并按照以下说明操作：

1. 选择 "**新建支持请求**"。
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. 在 "**服务**" 部分中，选择 "**存储帐户管理**" 并指定要迁移的帐户。
3. **다음**을 선택합니다.
4. **문제** 섹션에서 다음 값을 지정합니다.
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **类别**：选择 "**迁移到区域内的（RA-） GZRS**"。
    - **标题**：键入描述性标题，例如 **（RA-） GZRS 帐户迁移**。
    - **详细信息**：在 "**详细**信息" 框中键入其他详细信息，例如，"我想要从 \_\_ 区域中的 [LRS，GRS] 迁移到 GZRS。" 要从 \_\_ 区域中的 [LRS，RA-GRS] 迁移到 GZRS。 "
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

支持代表将与你联系以提供帮助。

## <a name="see-also"></a>참고 항목

- [Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [ZRS(영역 중복 스토리지): 고가용성 Azure Storage 애플리케이션](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
- [표준 스토리지 계정의 확장성 및 성능 목표](scalability-targets-standard-account.md)
