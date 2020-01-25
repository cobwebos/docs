---
title: 灾难恢复和存储帐户故障转移（预览版）
titleSuffix: Azure Storage
description: Azure Storage는 지역 중복 스토리지 계정에 대해 계정 장애 조치(미리 보기)를 지원합니다. 계정 장애 조치(failover)의 경우 기본 엔드포인트를 사용할 수 없는 경우 스토리지 계정에 대해 장애 조치(failover) 프로세스를 시작할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: bca780eb31a16d3854a2e4dfac821d5a68e9ca43
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719324"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>灾难恢复和帐户故障转移（预览版）

Microsoft는 Azure 서비스를 항상 사용할 수 있도록 하기 위해 노력합니다. 그러나 계획되지 않은 서비스 중단이 발생할 수 있습니다. 如果你的应用程序需要复原，Microsoft 建议使用异地冗余存储，以便将你的数据复制到第二个区域。 또한 고객은 지역 서비스 중단을 처리하기 위해 재해 복구 계획을 설정하는 것이 좋습니다. 재해 복구 계획의 중요한 부분은 기본 엔드포인트를 사용할 수 없는 경우 보조 엔드포인트로의 장애 조치(failover)를 준비하는 것입니다.

Azure Storage는 지역 중복 스토리지 계정에 대해 계정 장애 조치(미리 보기)를 지원합니다. 계정 장애 조치(failover)의 경우 기본 엔드포인트를 사용할 수 없는 경우 스토리지 계정에 대해 장애 조치(failover) 프로세스를 시작할 수 있습니다. 장애 조치(failover)는 스토리지 계정의 기본 엔드포인트가 되도록 보조 엔드포인트를 업데이트합니다. 장애 조치(failover)가 완료되면 클라이언트는 새 기본 엔드포인트에 쓰기 시작할 수 있습니다.

이 문서에서는 계정 장애 조치(failover)와 관련된 개념 및 프로세스를 설명하고 고객에게 최소의 영향만 미치고 복구할 수 있게 스토리지 계정을 준비하는 방법을 논의합니다. Azure Portal 또는 PowerShell에서 계정 장애 조치(failover)를 시작하는 방법을 알아보려면 [계정 장애 조치(failover) 시작(미리 보기)](storage-initiate-account-failover.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>적절한 중복 옵션 선택

Azure 存储空间维护存储帐户的多个副本，以确保持久性和高可用性。 계정에 대해 선택하는 중복 옵션은 필요한 복원력 수준에 따라 달라집니다. 지역 가동 중단으로부터 보호하려는 경우 보조 지역에서 읽기 액세스 옵션을 제공하거나 제공하지 않는 지역 중복 스토리지를 선택합니다.  

**异地冗余存储（GRS）或异地冗余存储（GZRS）（预览版）** 会以异步方式将数据复制到介于数百英里以外的两个地理区域。 주 지역에서 중단이 발생하는 경우 보조 지역이 데이터의 중복 원본으로 사용됩니다. 장애 조치(failover)를 시작하여 보조 엔드포인트를 기본 엔드포인트으로 변환할 수 있습니다.

**读取访问异地冗余存储（GRS）或读取访问权限异地冗余存储（GZRS）（预览版）** 提供了异地冗余存储，并具有对辅助终结点的读取访问权限。 기본 엔드포인트에서 중단이 발생하는 경우 RA-GRS용으로 구성되고 고가용성을 위해 설계된 애플리케이션이 보조 엔드포인트에서 계속 읽을 수 있습니다. 애플리케이션에 대해 최대 복원력을 보장하기 위해 RA-GRS를 사용하는 것이 좋습니다.

有关 Azure 存储空间中冗余的详细信息，请参阅[Azure 存储冗余](storage-redundancy.md)。

> [!WARNING]
> 지역 중복 스토리지를 사용하면 데이터가 손실될 위험이 있습니다. 数据将异步复制到次要区域，这意味着写入主要区域的数据写入次要区域之间存在延迟。 发生服务中断时，尚未复制到辅助终结点的主终结点的写入操作将丢失。

## <a name="design-for-high-availability"></a>고가용성을 위한 디자인

처음부터 고가용성을 위해 애플리케이션을 디자인하는 것이 중요합니다. 애플리케이션을 디자인하고 재해 복구를 계획하기 위한 지침에 대해서는 다음 Azure 리소스를 참조하세요.

- [设计适用于 azure 的弹性应用程序](/azure/architecture/checklist/resiliency-per-service)：概述在 azure 中构建高度可用的应用程序的关键概念。
- [可用性清单](/azure/architecture/checklist/resiliency-per-service)：用于验证应用程序是否实现了高可用性最佳设计实践的检查表。
- [使用 GRS 设计高度可用的应用程序](storage-designing-ha-apps-with-ragrs.md)：用于构建应用程序以利用 GRS 的设计指南。
- [教程：使用 Blob 存储构建高度可用的应用程序](../blobs/storage-create-geo-redundant-storage.md)：演示如何构建高度可用的应用程序，该应用程序可在发生故障时自动切换到终结点，并模拟恢复。 

또한 Azure Storage 데이터에 대해 고가용성을 유지하기 위한 다음 모범 사례도 참조하세요.

- **磁盘：** 使用[Azure 备份](https://azure.microsoft.com/services/backup/)来备份 Azure 虚拟机所用的 VM 磁盘。 또한 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 사용하여 지역 재해 발생 시 VM을 보호하는 것이 좋습니다.
- **块 blob：** 启用[软删除](../blobs/storage-blob-soft-delete.md)，通过[AzCopy](storage-use-azcopy.md)、 [Azure PowerShell](storage-powershell-guide-full.md)或[Azure 数据移动库](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)，防止对象级删除和覆盖，或将块 blob 复制到其他区域中的其他存储帐户。
- **文件：** 使用[AzCopy](storage-use-azcopy.md)或[Azure PowerShell](storage-powershell-guide-full.md)将文件复制到不同区域中的其他存储帐户。
- **테이블:** [AzCopy](storage-use-azcopy.md)를 사용하여 다른 지역에 있는 다른 스토리지 계정으로 테이블 데이터를 내보냅니다.

## <a name="track-outages"></a>중단 추적

고객은 [Azure Service Health 대시보드](https://azure.microsoft.com/status/)를 구독하여 Azure Storage 및 다른 Azure 서비스의 상태를 추적할 수 있습니다.

쓰기 오류의 가능성에 대비하도록 애플리케이션을 디자인하는 것이 좋습니다. 애플리케이션은 주 지역의 가동 중단 가능성을 경고하는 방식으로 쓰기 오류를 공개해야 합니다.

## <a name="understand-the-account-failover-process"></a>계정 장애 조치(failover) 프로세스 이해

고객이 관리하는 계정 장애 조치(failover)(미리 보기)를 사용하면 어떤 이유로든 주 지역을 사용할 수 없게 될 경우 전체 스토리지 계정을 보조 지역으로 장애 조치(failover)할 수 있습니다. 강제로 보조 지역으로 장애 조치(failover)하는 경우 클라이언트는 장애 조치(failover)가 완료된 후에 보조 엔드포인트에 데이터를 쓰기 시작할 수 있습니다. 장애 조치(failover)에는 일반적으로 약 1시간이 걸립니다.

### <a name="how-an-account-failover-works"></a>계정 장애 조치(failover) 작동 방법

在正常情况下，客户端会将数据写入主要区域中的 Azure 存储帐户，并将数据异步复制到次要区域。 다음 그림은 주 지역을 사용할 수 있는 경우의 시나리오를 보여 줍니다.

![클라이언트가 주 지역의 스토리지 계정에 데이터를 기록합니다.](media/storage-disaster-recovery-guidance/primary-available.png)

어떤 이유로든 기본 엔드포인트를 사용할 수 없는 경우 클라이언트는 스토리지 계정에 더 이상 쓸 수 없습니다. 다음 그림은 기본 엔드포인트를 사용할 수 없지만 복구가 아직 발생하지 않은 시나리오를 보여 줍니다.

![기본 엔드포인트를 사용할 수 없으므로 클라이언트가 데이터를 쓸 수 없습니다.](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

고객은 보조 엔드포인트에 대한 계정 장애 조치(failover)를 시작합니다. 장애 조치(failover) 프로세스는 Azure Storage에서 제공하는 DNS 항목을 업데이트하므로 다음 그림에 나와 있는 것처럼 보조 엔드포인트가 스토리지 계정의 새 기본 엔드포인트가 됩니다.

![고객은 보조 엔드포인트로의 계정 장애 조치(failover)를 시작합니다.](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

DNS 항목이 업데이트되면 GRS 및 RA-GRS 계정의 쓰기 액세스 권한이 복원되고 요청이 새 기본 엔드포인트로 전달됩니다. Blob, 테이블, 큐 및 파일에 대한 기존 스토리지 서비스 엔드포인트는 장애 조치(failover) 후에도 동일하게 유지됩니다.

> [!IMPORTANT]
> 장애 조치(failover)를 완료한 후 스토리지 계정은 새 기본 엔드포인트에서 로컬로 중복되도록 구성됩니다. 새 보조 엔드포인트로의 복제를 재개하려면 지역 중복 스토리지를 다시 사용하도록 계정을 구성합니다(RA-GRS 또는 GRS).
>
> LRS 계정을 RA-GRS 또는 GRS로 변환하면 비용이 발생합니다. 이 비용은 장애 조치(failover) 후에 RA-GRS 또는 GRS를 사용하도록 새 주 지역의 스토리지 계정을 업데이트할 경우에 적용됩니다.  

### <a name="anticipate-data-loss"></a>데이터 손실 예상

> [!CAUTION]
> 계정 장애 조치(Failover)를 수행하면 일반적으로 데이터가 일부 손실됩니다. 계정 장애 조치(failover)를 시작할 때 진행되는 과정을 이해하는 것이 중요합니다.  

由于将数据从主要区域异步写入次要区域，因此在将主要区域写入到次要区域之前，总会有一段延迟。 如果主要区域变得不可用，则最近的写入可能尚未复制到次要区域。

강제로 장애 조치(failover)를 수행하면 보조 지역이 새로운 주 지역이 되고 스토리지 계정이 로컬로 중복되도록 구성되므로 주 지역의 모든 데이터가 손실됩니다. 发生故障转移时，已复制到辅助副本的所有数据都将保留。 但是，写入到未复制到辅助副本的主副本的任何数据都将永久丢失。

**마지막 동기화 시간** 속성은 주 지역의 데이터가 보조 지역에 기록될 것으로 보장되는 가장 최근 시간을 나타냅니다. 마지막 동기화 시간 전에 기록된 모든 데이터는 보조 지역에서 사용할 수 있지만, 마지막 동기화 시간 이후에 기록된 데이터는 보조 지역에 기록되지 않았을 수 있으므로 손실될 수 있습니다. 작동 중단 시 이 속성을 사용하여 계정 장애 조치(failover) 시작으로 인해 발생할 수 있는 데이터 손실 크기를 예상합니다.

모범 사례로, 마지막 동기화 시간을 사용하여 예상되는 데이터 손실을 예측할 수 있도록 애플리케이션을 디자인합니다. 예를 들어, 모든 쓰기 작업을 기록하는 경우 마지막 쓰기 작업의 시간을 마지막 동기화 시간과 비교하여 보조 지역과 동기화되지 않은 쓰기를 확인할 수 있습니다.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>원래 주 지역으로 장애 복구(Failback)할 때 주의

주 지역에서 보조 지역으로 장애 조치(failover)한 후에 스토리지 계정이 새 주 지역에서 로컬로 중복되도록 구성됩니다. GRS 또는 RA-GRS를 사용하도록 업데이트하여 지리적 중복성을 위해 계정을 다시 구성할 수 있습니다. 如果在故障转移后再次为该帐户配置了异地冗余，则新的主要区域会立即开始将数据复制到新的次要区域，这是原始故障转移前的主区域。 但是，在主数据库中的现有数据完全复制到新的辅助数据库之前，可能需要一段时间。

스토리지 계정이 지역 복제를 위해 다시 구성되면 새 주 지역에서 새 보조 지역으로의 다른 장애 조치(failover)를 시작할 수 있습니다. 이 경우 장애 조치(failover) 전의 원래 주 지역이 다시 주 지역이 되고 로컬로 중복되도록 구성됩니다. 장애 조치(failover) 후 주 지역(원래 보조 지역)의 모든 데이터는 손실됩니다. 如果在故障回复之前，存储帐户中的大部分数据尚未复制到新的辅助数据库，则可能会丢失重大数据。

주요 데이터 손실을 방지하려면 장애 복구(failback) 전에 **마지막 동기화 시간** 속성 값을 확인합니다. 마지막 동기화 시간을 데이터를 새 주 지역에 마지막으로 기록한 시간과 비교하여 데이터 손실을 예측합니다. 

## <a name="initiate-an-account-failover"></a>계정 장애 조치(failover) 시작

Azure Portal, PowerShell, Azure CLI 또는 Azure Storage 리소스 공급자 API에서 계정 장애 조치(failover)를 시작할 수 있습니다. 장애 조치(failover)를 시작하는 방법에 대한 자세한 내용은 [계정 장애 조치(failover) 시작(미리 보기)](storage-initiate-account-failover.md)을 참조하세요.

## <a name="about-the-preview"></a>미리 보기 정보

对于在 Azure 资源管理器部署中使用 GRS 或 GRS 的所有客户，可通过预览版使用帐户故障转移。 범용 v1, 범용 v2 및 Blob 스토리지 계정 유형을 사용할 수 있습니다. 계정 장애 조치(failover)는 현재 다음 지역에서 사용할 수 있습니다.

- 아시아 동부
- 동남아시아
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 미국 중부
- 미국 동부 2
- 미국 중서부
- 미국 서부 2

이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

### <a name="register-for-the-preview"></a>미리 보기에 등록

미리 보기에 등록하려면 PowerShell에서 다음 명령을 실행합니다. 대괄호 안의 자리 표시자를 사용자 고유의 구독 ID로 바꾸어야 합니다.

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

需要5-7 天才能接收预览版批准。 등록이 승인되었는지 확인하려면 다음 명령을 실행합니다.

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>기타 고려 사항

미리 보기 기간 동안 강제 장애 조치(failover)를 수행할 경우 애플리케이션 및 서비스가 어떤 영향을 받을 수 있는지를 이해하려면 이 섹션에 설명된 추가 고려 사항을 검토합니다.

#### <a name="storage-resource-provider"></a>스토리지 리소스 공급자

故障转移完成后，客户端可再次读取并写入新的主要区域中的 Azure 存储数据。 但是，Azure 存储资源提供程序不会进行故障转移，因此资源管理操作仍必须在主要区域中发生。 如果主要区域不可用，将无法对存储帐户执行管理操作。

由于 Azure 存储资源提供程序不进行故障转移，因此在故障转移完成后， [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location)属性将返回原始主位置。

#### <a name="azure-virtual-machines"></a>Azure 가상 머신

Azure VM(Virtual Machines)은 계정 장애 조치(failover)의 일부로 장애 조치(failover)되지 않습니다. 주 지역을 사용할 수 없으며 보조 지역으로 장애 조치(failover)할 경우 장애 조치(failover) 후에 VM을 다시 만들어야 합니다. 此外，还可能会丢失与帐户故障转移相关联的数据。 Microsoft 建议特定于 Azure 中的虚拟机的以下[高可用性](../../virtual-machines/windows/manage-availability.md)和[灾难恢复](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md)指南。

#### <a name="azure-unmanaged-disks"></a>Azure 관리되지 않는 디스크

모범 사례로, 관리되지 않는 디스크를 Managed Disks로 변환하는 것이 좋습니다. 그러나 Azure VM에 연결된 관리되지 않는 디스크를 포함하는 계정을 장애 조치(failover)해야 할 경우 장애 조치(failover)를 시작하기 전에 VM을 종료해야 합니다.

관리되지 않는 디스크는 Azure Storage에 페이지 Blob으로 저장됩니다. VM을 Azure에서 실행 중인 경우 VM에 연결된 모든 관리되지 않는 디스크가 임대됩니다. Blob에 임대가 있으면 계정 장애 조치(failover)를 계속할 수 없습니다. 장애 조치(failover)를 수행하려면 다음 단계를 수행합니다.

1. 시작하기 전에 관리되지 않는 디스크, 해당 LUN(논리 단위 번호) 및 해당 디스크가 연결된 VM을 기록해 둡니다. 이렇게 하면 장애 조치(failover) 후에 디스크를 보다 쉽게 다시 연결할 수 있습니다.
2. VM을 종료합니다.
3. VM을 삭제하지만 관리되지 않는 디스크의 VHD 파일은 유지합니다. VM을 삭제한 시간을 기록해 둡니다.
4. VM을 삭제한 이후에 해당하는 **마지막 동기화 시간**이 업데이트될 때까지 기다립니다. 장애 조치(failover)가 발생할 때 보조 엔드포인트가 VHD 파일로 완전히 업데이트되지 않은 경우 새 주 지역에서 VM이 제대로 작동하지 않을 수 있으므로 이 단계가 필요합니다.
5. 계정 장애 조치(failover)를 시작합니다.
6. 계정 장애 조치(failover)가 완료되고 보조 지역이 새 주 지역이 될 때까지 기다립니다.
7. 새 주 지역에서 VM을 만들고 VHD를 다시 연결합니다.
8. 새 VM을 시작합니다.

VM이 종료되면 임시 디스크에 저장된 데이터가 손실됩니다.

### <a name="unsupported-features-and-services"></a>不支持的功能和服务

预览版本的帐户故障转移不支持以下功能和服务：

- Azure 파일 동기화는 스토리지 계정 장애 조치(Failover)를 지원하지 않습니다. Azure 파일 동기화에서 클라우드 엔드포인트로 사용되는 Azure 파일 공유를 포함하는 스토리지 계정은 장애 조치(failover)하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다.  
- 보관된 blob을 포함하는 스토리지 계정은 장애 조치(failover)할 수 없습니다. 보관된 blob은 장애 조치(failover)하지 않으려는 별도 스토리지 계정에 유지 관리합니다.
- 프리미엄 블록 blob을 포함하는 스토리지 계정은 장애 조치(failover)할 수 없습니다. 프리미엄 블록 blob를 지원하는 스토리지 계정은 현재 지리적 중복을 지원하지 않습니다.
- 不能对包含任何启用了[蠕虫永久性策略](../blobs/storage-blob-immutable-storage.md)的容器的存储帐户进行故障转移。 已解锁/锁定的基于时间的保留或法律封存策略阻止故障转移，以便保持符合性。
- 故障转移完成后，以下功能可能会在初始启用时停止工作：[事件订阅](../blobs/storage-blob-event-overview.md)、[更改源](../blobs/storage-blob-change-feed.md)、[生命周期策略](../blobs/storage-lifecycle-management-concepts.md)和[存储分析日志记录](storage-analytics-logging.md)。

## <a name="copying-data-as-an-alternative-to-failover"></a>장애 조치(failover) 대신 데이터 복사

스토리지 계정이 RA-GRS에 대해 구성되면 보조 엔드포인트를 사용하여 데이터에 대해 읽기 액세스 권한을 갖게 됩니다. 주 지역에서 중단이 발생할 경우 장애 조치(failover)하지 않으려는 경우 [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) 또는 [Azure 데이터 이동 라이브러리](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)와 같은 도구를 사용하여 보조 지역의 스토리지 계정에 있는 데이터를 영향을 받지 않는 지역의 다른 스토리지 계정으로 복사할 수 있습니다. 그런 다음, 읽기 및 쓰기 가용성을 위해 애플리케이션에서 해당 스토리지 계정을 가리키도록 할 수 있습니다.

> [!CAUTION]
> 不应在数据迁移策略中使用帐户故障转移。


## <a name="microsoft-managed-failover"></a>Microsoft에서 관리하는 장애 조치(failover)

중대한 재해로 인해 지역이 손실되는 극단적인 경우 Microsoft는 지역 장애 조치(failover)를 시작할 수 있습니다. 이 경우에 사용자의 조치가 필요하지 않습니다. Microsoft에서 관리하는 장애 조치(failover)가 완료될 때까지 스토리지 계정에 대한 쓰기 액세스 권한이 없습니다. 애플리케이션은 스토리지 계정이 RA-GRS용으로 구성된 경우 보조 지역에서 읽을 수 있습니다. 

## <a name="see-also"></a>참고 항목

- [계정 장애 조치(failover) 시작(미리 보기)](storage-initiate-account-failover.md)
- [RA-GRS를 사용하여 항상 사용 가능한 애플리케이션 디자인](storage-designing-ha-apps-with-ragrs.md)
- [教程：使用 Blob 存储构建高度可用的应用程序](../blobs/storage-create-geo-redundant-storage.md) 
