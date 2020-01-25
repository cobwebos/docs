---
title: 使用区域冗余存储（ZRS）构建高度可用的应用程序
titleSuffix: Azure Storage
description: ZRS(영역 중복 스토리지)는 고가용성 애플리케이션을 빌드하는 간단한 방법을 제공합니다. ZRS는 데이터센터의 하드웨어 오류뿐 아니라 일부 지역적인 재해로부터 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 0e6b87ff34d6555fda50518198f9ae3839aa56e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719086"
---
# <a name="build-highly-available-applications-with-zone-redundant-storage-zrs"></a>使用区域冗余存储（ZRS）构建高度可用的应用程序

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>지원 범위 및 국가별 가용성

ZRS 目前支持标准常规用途 v2、FileStorage 和 BlockBlobStorage 存储帐户类型。 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

常规用途 v2 ZRS 帐户支持块 blob、非磁盘页 blob、标准文件共享、表和队列。

对于常规用途 v2 帐户，ZRS 已在以下区域公开发布：

- 동남아시아
- 북유럽
- 서유럽
- 프랑스 중부
- 일본 동부
- 남아프리카 공화국 북부
- 영국 남부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 서부 2

对于 FileStorage 帐户（高级文件共享）和 BlockBlobStorage 帐户（高级块 blob），ZRS 在以下区域公开提供：

- 서유럽
- 미국 동부

Microsoft는 추가 Azure 지역에서 ZRS를 사용할 수 있도록 계속 지원하고 있습니다. 새 지역에 대한 정보는 [Azure 서비스 업데이트](https://azure.microsoft.com/updates/) 페이지에서 정기적으로 확인하세요.

**已知限制**

- ZRS 帐户当前不支持存档层。 有关更多详细信息，请参阅[Azure Blob 存储：热、冷和存档访问层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)。
- 托管磁盘不支持 ZRS。 可以将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并在[LRS 和 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>영역을 사용할 수 없게 되면 어떻게 되나요?

영역을 사용할 수 없게 되는 경우에도 읽기 및 쓰기 작업에는 계속 액세스할 수 있습니다. 일시적인 오류 처리에 대한 지침을 계속 따르는 것이 좋습니다. 이러한 방식에는 지수적 백오프를 사용하여 재시도 정책을 구현하는 것이 포함됩니다.

영역을 사용할 수 없는 경우 Azure에서 DNS 재지정과 같은 네트워킹 업데이트를 수행합니다. 업데이트가 완료되기 전에 데이터에 액세스하는 경우 이러한 업데이트는 애플리케이션에 영향을 줄 수 있습니다.

ZRS는 여러 영역에 영구적인 영향을 주는 지역 재해로부터 데이터를 보호하지 못할 수 있습니다. 대신, 데이터를 일시적으로 사용할 수 없게 되면 ZRS에서 해당 데이터에 대한 복원력을 제공합니다. 지역 재해로부터 보호하려면 GRS(지역 중복 스토리지)를 사용하는 것이 좋습니다. GRS에 대한 자세한 내용은 [GRS(지역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)를 참조하세요.

## <a name="converting-to-zrs-replication"></a>ZRS 복제로 변환

LRS, GRS 및 RA-GRS 간에 마이그레이션하는 것은 간단합니다. Azure Portal 또는 Storage 리소스 공급자 API를 사용하여 계정의 중복 유형을 변경합니다. 그런 다음, Azure에서는 데이터를 적절하게 복제합니다. 

将数据迁移到 ZRS 需要其他策略。 ZRS 마이그레이션에는 단일 스토리지 스탬프에서 지역 내의 여러 스탬프로 데이터의 실제 이동이 포함됩니다.

迁移到 ZRS 有两个主要选项： 

- 기존 계정에서 새 ZRS 계정으로 데이터를 수동으로 복사하거나 이동할 수 있습니다.
- 실시간 마이그레이션을 요청합니다.

> [!IMPORTANT]
> 高级文件共享目前不支持实时迁移。 目前仅支持手动复制或移动数据。

특정 날짜에 마이그레이션을 완료해야 하는 경우 수동 마이그레이션을 수행하는 것이 좋습니다. 수동 마이그레이션은 실시간 마이그레이션보다 더 유연합니다. 수동 마이그레이션을 사용하면 타이밍을 제어할 수 있습니다.

수동 마이그레이션을 수행하려면 다음과 같은 옵션이 있습니다.
- Azure Storage 클라이언트 라이브러리 중 하나인 AzCopy와 같은 기존 도구나 신뢰할 수 있는 타사 도구를 사용합니다.
- Hadoop 또는 HDInsight와 친숙한 경우 원본 및 대상(ZRS) 계정 모두를 클러스터에 연결합니다. 그런 다음, DistCp와 같은 도구를 사용하여 데이터 복사 프로세스를 병렬 처리합니다.
- Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 도구를 빌드합니다.

수동 마이그레이션을 사용하면 결국 애플리케이션 가동 중지 시간이 발생할 수 있습니다. 애플리케이션에 고가용성이 필요한 경우 Microsoft에서는 실시간 마이그레이션 옵션도 제공합니다. 실시간 마이그레이션은 가동 중지 시간이 없는 즉각적인 마이그레이션입니다. 

실시간 마이그레이션 중 원본 및 대상 스토리지 스탬프 간에 데이터를 마이그레이션하면서 스토리지 계정을 사용할 수 있습니다. 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

실시간 마이그레이션에 대한 다음과 같은 제한에 유의하십시오.

- Microsoft에서는 실시간 마이그레이션에 대한 요청을 신속하게 처리하지만 마이그레이션이 완료되는 시기는 보장하지 않습니다. 특정 날짜에 ZRS로 마이그레이션된 데이터가 필요한 경우에는 대신 수동 마이그레이션을 수행하는 것이 좋습니다. 일반적으로 계정에 데이터가 많을수록 해당 데이터를 마이그레이션하는 데 시간이 더 오래 걸립니다. 
- 只有使用 LRS 复制的存储帐户才支持实时迁移。 如果你的帐户使用 GRS 或 GRS，则需要先将帐户的复制类型更改为 LRS，然后再继续。 此中间步骤将删除 GRS/GRS 所提供的辅助终结点。
- 계정에 데이터가 있어야 합니다.
- 동일한 지역 내에서만 데이터를 마이그레이션할 수 있습니다. 데이터를 원본 계정과 다른 지역에 있는 ZRS 계정으로 마이그레이션하려면 수동 마이그레이션을 수행해야 합니다.
- 표준 스토리지 계정 유형만 실시간 마이그레이션을 지원합니다. 프리미엄 스토리지 계정은 수동으로 마이그레이션해야 합니다.
- 不支持从 ZRS 到 LRS、GRS 或 RA-GRS 的实时迁移。 데이터를 수동으로 새로운 스토리지 계정 또는 기존 스토리지 계정으로 이동해야 합니다.
- 托管磁盘仅适用于 LRS，无法迁移到 ZRS。 可以将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并在[LRS 和 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。 有关与可用性集的集成，请参阅[Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)。
- 包含存档数据的 LRS 或 GRS 帐户无法迁移到 ZRS。

실시간 마이그레이션은 [Azure 지원 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)을 통해 요청할 수 있습니다. 포털에서 ZRS로 변환하려는 스토리지 계정을 선택합니다.
1. **새 지원 요청** 선택
2. 사용자 계정 정보를 기반으로 **기본 사항**을 완료합니다. **서비스** 섹션에서 **스토리지 계정 관리** 및 ZRS로 변환하려는 리소스를 선택합니다. 
3. **다음**을 선택합니다. 
4. **문제** 섹션에서 다음 값을 지정합니다. 
    - **심각도**: 기본값을 그대로 둡니다.
    - **문제 유형**: **데이터 마이그레이션**을 선택합니다.
    - **类别**：选择**迁移到 ZRS**。
    - **제목**: 설명이 포함된 제목, 예를 들어 **ZRS 계정 마이그레이션**을 입력합니다.
    - **详细信息**：在 "**详细**信息" 框中键入其他详细信息，例如，我想要从 \_\_ 区域中的 [LRS，GRS] 迁移到 ZRS。 
5. **다음**을 선택합니다.
6. **연락처 정보** 블레이드에서 연락처 정보가 올바른지 확인합니다.
7. **만들기**를 선택합니다.

지원 담당자가 사용자에게 연락하여 필요한 모든 지원을 제공합니다.

## <a name="live-migration-to-zrs-faq"></a>实时迁移到 ZRS 常见问题

**是否应该在迁移期间计划停机时间？**

迁移不会造成停机。 在实时迁移期间，你可以继续使用存储帐户，同时在源和目标存储 stamp 之间迁移数据。 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

**迁移是否有任何数据丢失？**

与迁移关联的数据不会丢失。 마이그레이션 프로세스 중에 일반적으로 수행하는 것과 동일한 수준의 내구성 및 가용성 SLA가 유지됩니다.

**迁移完成后，是否需要对应用程序进行任何更新？**

迁移完成后，帐户的复制类型将更改为 "区域冗余存储（ZRS）"。 服务终结点、访问密钥、SAS 和任何其他帐户配置选项保持不变并保持不变。

**我是否能请求将常规用途 v1 帐户实时迁移到 ZRS？**

ZRS 仅支持常规用途 v2 帐户，以便在将实时迁移的请求提交到 ZRS 之前，请确保将你的帐户升级到常规用途 v2。 有关更多详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)和[升级到常规用途 v2 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)。

**能否向 ZRS 请求异地冗余或读取访问异地冗余存储（GRS/GRS）帐户的实时迁移？**

只有使用 LRS 复制的存储帐户才支持实时迁移。 如果你的帐户使用 GRS 或 GRS，则需要先将帐户的复制类型更改为 LRS，然后再继续。 此中间步骤将删除 GRS/GRS 所提供的辅助终结点。 此外，在将实时迁移请求提交到 ZRS 之前，请确保你的应用程序或工作负荷不再需要访问辅助只读终结点，并将你的存储帐户的复制类型更改为本地冗余存储（LRS）。 有关更多详细信息，请参阅[更改复制策略](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy)。

**我是否可以请求将我的存储帐户的实时迁移到另一个区域 ZRS？**

如果要将数据迁移到不同于源帐户区域的区域中的 ZRS 帐户，必须执行手动迁移。

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS 클래식: 블록 Blob 중복성을 위한 레거시 옵션
> [!NOTE]
> Microsoft에서는 2021년 3월 31일에 ZRS 클래식 계정의 사용을 중단하고 마이그레이션할 예정입니다. 사용이 중단되기 전에 ZRS 클래식 고객에게 자세한 내용이 제공됩니다. 
>
> ZRS 在某个区域[公开上市](#support-coverage-and-regional-availability)后，客户将无法在该区域的门户中创建 ZRS 经典帐户。 ZRS 클래식의 사용이 중단될 때까지 Microsoft PowerShell 및 Azure CLI를 사용하여 ZRS 클래식 계정을 만드는 것은 옵션입니다.

ZRS 클래식은 1~2개의 지역 내의 데이터 센터에서 데이터를 비동기적으로 복제합니다. 복제된 데이터는 Microsoft가 보조 지역에 장애 조치(failover)를 시작하지 않는 한 사용할 수 없습니다. ZRS 클래식 계정을 LRS, GRS 또는 RA-GRS 계정으로 변환하거나, 이러한 계정에서 ZRS 클래식 계정으로 변환할 수 없습니다. 또한 ZRS 클래식 계정은 메트릭이나 로깅을 지원하지 않습니다.

ZRS 클래식은 GPv1(범용 V1) 스토리지 계정의 **블록 Blob**에서만 사용할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

LRS, ZRS 클래식, GRS 또는 RA-GRS 계정 간에 ZRS 계정 데이터를 수동으로 마이그레이션하려면 AzCopy, Azure Storage Explorer, Azure PowerShell 또는 Azure CLI와 같은 도구 중 하나를 사용합니다. Azure Storage 클라이언트 라이브러리 중 하나를 사용하여 사용자 고유의 마이그레이션 솔루션을 빌드할 수도 있습니다.

你还可以在门户中将 ZRS 经典帐户升级到 ZRS，或使用 ZRS 提供的区域中的 Azure PowerShell 或 Azure CLI。 若要升级到 Azure 门户中的 ZRS，请导航到该帐户的 "**配置**" 部分，然后选择 "**升级**"：

![将 ZRS 经典升级到门户中的 ZRS](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

若要使用 PowerShell 升级到 ZRS，请调用以下命令：
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

若要使用 CLI 升级到 ZRS，请调用以下命令：
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>참고 항목
- [Azure Storage 복제](storage-redundancy.md)
- [LRS(로컬 중복 스토리지): Azure Storage에 대한 저렴한 데이터 중복](storage-redundancy-lrs.md)
- [GRS(지역 중복 스토리지): Azure Storage에 대한 지역 간 복제](storage-redundancy-grs.md)
