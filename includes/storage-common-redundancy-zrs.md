---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 86c301748b58e7642df9a738c70b4fe70be3310b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400002"
---
区域冗余存储 (ZRS) 以同步方式在单个区域中的三个存储群集之间复制数据。 各个存储群集以物理方式相互隔离，并驻留在自身的可用性区域 (AZ) 中。 每个可用性区域及其中的 ZRS 群集是自主性的，具有独立的实用工具和网络功能。

将数据存储在 ZRS 帐户中可以确保当某个区域不可用时，能够访问和管理自己的数据。 ZRS 提供卓越的性能和较低的延迟。 ZRS 提供与[本地冗余存储 (LRS)](../articles/storage/common/storage-redundancy-lrs.md) 相同的[可伸缩性目标](../articles/storage/common/storage-scalability-targets.md)。

如果场景中要求满足以下要求，请考虑使用 ZRS：即使服务中断或自然灾难导致局域数据中心不可用，也要求提供强一致性、强持久性和高可用性。 ZRS 在一年中提供至少 99.9999999999%（12 个 9）的存储对象持久性。

有关可用性区域的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

## <a name="support-coverage-and-regional-availability"></a>支持覆盖范围和区域可用性
ZRS 目前支持标准的[常规用途 v2 (GPv2)](../articles/storage/common/storage-account-options.md#general-purpose-v2-accounts) 帐户类型。 ZRS 适用于块 Blob、非磁盘页 Blob、文件、表和队列。 此外，支持所有的[存储分析](../articles/storage/common/storage-analytics.md)日志和[存储指标](../articles/storage/common/storage-enable-and-view-metrics.md)

ZRS 在以下区域推出了正式版：

- 美国东部 2
- 美国西部 2
- 美国中部
- 北欧
- 西欧
- 法国中部
- 东南亚

Microsoft 将继续在其他 Azure 区域推出 ZRS。 请不时地查看 [Azure 服务更新](https://azure.microsoft.com/updates/)来了解有关新区域的信息。

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>当一个区域不可用时，会发生什么情况？

如果某个区域不可用，数据仍保持复原能力。 Microsoft 建议持续遵循暂时性故障的处理做法，例如，结合指数退让实施重试策略。 当某个区域不可用时，Azure 会执行网络更新，例如 DNS 重新指向。 如果完成更新之前访问数据，这些更新可能会影响应用程序。

如果多个区域永久受到影响，在发生区域性的灾难时，ZRS 可能无法保护数据。 如果区域只是暂时不可用，则 ZRS 可以提供数据复原能力。 为了在发生区域性灾难时提供保护，Microsoft 建议使用异地冗余存储 (GRS)。 有关 GRS 的详细信息，请参阅[异地冗余存储 (GRS)：Azure 存储的跨区域复制](../articles/storage/common/storage-redundancy-grs.md)。

## <a name="converting-to-zrs-replication"></a>转换为 ZRS 复制
目前，只要迁移到 LRS、GRS 和 RA-GRS 或从中迁移，就能使用 Azure 门户或存储资源提供程序 API 来更改帐户的冗余类型。 但是，使用 ZRS 进行迁移并不那么直接，因为这涉及到将数据从单个存储堆栈物理迁移到某个区域中的多个堆栈。 

可以使用两个主要选项迁移到 ZRS 或从中迁移。 可以手动将现有帐户中的数据复制或移动到新的 ZRS 帐户。 还可以请求实时迁移。 Microsoft 强烈建议执行手动迁移，因为无法保证何时能够完成实时迁移。 与实时迁移相比，手动迁移路线提供更大的灵活性，而且可以控制迁移的时限。

若要执行手动迁移，可以使用多种选项：
- 使用现有的工具（例如 AzCopy）、存储 SDK、可靠的第三方工具，等等。
- 如果你熟悉 Hadoop 或 HDInsight，则可将源和目标 (ZRS) 帐户附加到群集，并使用 DistCp 等工具来大规模并行化数据复制过程
- 利用某种形式的存储 SDK 来生成工具

但是，如果手动迁移会导致应用程序停机，而你无法在自己的一端承受这种停机，则可以使用 Microsoft 提供的实时迁移选项。 实时迁移是一种就地迁移方法，可让你在源与目标存储堆栈之间迁移数据的同时，继续使用现有的存储帐户。 在迁移期间，仍可获得平时一样的持久性和可用性 SLA 保障。

但是，实时迁移确实存在一些限制。 下面列出了这些限制。

- 尽管 Microsoft 会尽快解决实时迁移请求，但却无法保证迁移何时完成。 如果需要在特定的时间之前将数据放入 ZRS，应执行手动迁移。 通常，帐户中的数据越多，迁移这些数据所需的时间就越长。 
- 只能使用 LRS 或 GRS 复制从某个帐户执行实时迁移。 如果帐户使用 RA-GRS，则需要先迁移到这些复制类型之一，然后继续。 此中间步骤可确保在迁移之前，删除 RA-GRS 提供的辅助只读终结点。
- 帐户必须包含数据。
- 仅支持区域内部迁移。 若要将数据迁移到不包含源帐户的区域中的 ZRS 帐户，则必须执行手动迁移。
- 仅支持标准存储帐户类型。 无法从高级存储帐户迁移。

实时迁移请求会通过 Azure 支持门户。 在门户中，选择要转换为 ZRS 的存储帐户。
1. 单击“新建支持请求”
2. 确认“基本信息”。 单击“下一步”。 
3. 在“问题”部分， 
    - 将“严重性”保留原样。
    - 问题类型 = **数据迁移**
    - 类别 = **迁移到区域中的 ZRS**
    - 标题 = **ZRS 帐户迁移**（或描述性的文本）
    - 详细信息 = 我想要从 ______ 区域中的 [LRS, GRS] 迁移到 ZRS。 
4. 单击“下一步”。
5. 确认“联系信息”边栏选项卡中的联系信息是否正确。
6. 单击“提交”。

然后，支持人员会与你取得联系。 该支持人员将为你提供所需的任何帮助。 

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS 经典版：用于块 Blob 冗余的传统选项
> [!NOTE]
> ZRS 经典版帐户计划于 2021 年 3 月 31 日弃用并需要迁移。 Microsoft 会在弃用之前向 ZRS 经典版客户发送更多详细信息。 Microsoft 计划将来提供从 ZRS 经典版到 ZRS 的自动迁移过程。

>[!NOTE]
> ZRS 在某个区域推出[正式版](#support-coverage-and-regional-availability)之后，你不再可以在该区域中通过门户创建 ZRS 经典帐户。 但是，仍可以通过其他方式（例如 Microsoft PowerShell 和 Azure CLI）创建此类帐户，直到 ZRS 经典版已弃用。

ZRS 经典版以异步方式在一到两个区域中的数据中心之间复制数据。 除非 Microsoft 启动了到次要区域的故障转移，否则副本可能不可用。 ZRS 经典版仅适用于[常规用途 V1 (GPv1)](../articles/storage/common/storage-account-options.md#general-purpose-v1-accounts) 存储帐户中的**块 Blob**。 ZRS 经典版帐户不能与 LRS 或 GRS 相互转换，并且不提供指标或日志记录功能。

ZRS 经典版帐户无法与 LRS、GRS 或 RA-GRS 相互转换。 ZRS 经典版帐户也不支持指标或日志记录。

若要向/从 LRS、ZRS 经典版、GRS 或 RA-GRS 帐户手动迁移 ZRS 帐户数据，请使用 AzCopy、Azure 存储资源管理器、Azure PowerShell 或 Azure CLI。 此外，可以使用某个 Azure 存储客户端库生成自己的迁移解决方案。
