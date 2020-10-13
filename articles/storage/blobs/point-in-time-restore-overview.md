---
title: 块 blob 的时间点还原
titleSuffix: Azure Storage
description: 块 blob 的时间点还原通过使你能够在给定的时间点将存储帐户还原到其以前的状态，来防止意外删除或损坏。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aed3116976d57df81da399495fd9da3722ba770a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960671"
---
# <a name="point-in-time-restore-for-block-blobs"></a>块 blob 的时间点还原

时间点还原通过使你能够将块 blob 数据还原到更早的状态来防止意外删除或损坏。 当用户或应用程序意外删除数据或应用程序错误损坏数据的情况下，时间点还原非常有用。 时间点还原还支持测试方案，这些方案需要在运行其他测试之前将数据集还原到已知状态。

仅限常规用途 v2 存储帐户支持时间点还原。 只有热访问层和冷访问层中的数据才能使用时间点还原进行还原。

若要了解如何对存储帐户启用时间点还原，请参阅 [在块 blob 数据上执行时点还原](point-in-time-restore-manage.md)。

## <a name="how-point-in-time-restore-works"></a>时间点还原的工作原理

若要启用时间点还原，请创建存储帐户的管理策略并指定保持期。 在保留期内，可以将块 blob 从当前状态还原到上一个时间点的状态。

若要启动时间点还原，请调用 [还原 Blob 范围](/rest/api/storagerp/storageaccounts/restoreblobranges) 操作并指定 UTC 时间的还原点。 您可以指定要还原的容器和 blob 名称的字典范围，或省略存储帐户中所有容器的还原范围。 每个还原操作最多支持10个字典范围。

Azure 存储会分析在所请求的还原点（UTC 时间）和当前时间段内指定的 blob 所做的所有更改。 还原操作是原子操作，因此要么完全还原所有更改，要么失败。 如果存在无法还原的 blob，则操作将失败，并且会恢复受影响的容器的读取和写入操作。

一次只能在一个存储帐户上运行一个还原操作。 还原操作在执行后将无法取消，但可以执行第二个还原操作来撤消第一个操作。

" **还原 Blob 范围** " 操作返回唯一标识操作的还原 ID。 若要检查时间点还原的状态，请使用**还原 Blob 范围**操作返回的还原 ID 调用 "**获取还原状态**" 操作。

> [!IMPORTANT]
> 执行还原操作时，Azure 存储会阻止在操作期间还原的范围内的 blob 上的数据操作。 读取、写入和删除操作在主位置中被阻止。 出于此原因，在执行还原操作时，Azure 门户中的操作（如列出容器）可能不会按预期执行。
>
> 如果存储帐户是异地复制的，则在还原操作期间，从辅助位置读取操作可能会继续。

> [!CAUTION]
> 时间点还原仅支持对块 blob 执行还原操作。 无法还原对容器的操作。 如果通过调用 [删除容器](/rest/api/storageservices/delete-container) 操作从存储帐户中删除容器，则无法使用还原操作来还原该容器。 如果你可能希望还原各个 blob，则不会删除容器。

### <a name="prerequisites-for-point-in-time-restore"></a>时间点还原的先决条件

执行时点还原需要启用以下 Azure 存储功能，然后才能启用时间点还原：

- [软删除](soft-delete-overview.md)
- [更改源](storage-blob-change-feed.md)
- [Blob 版本控制](versioning-overview.md)

### <a name="retention-period-for-point-in-time-restore"></a>时间点还原的保留期

当你为存储帐户启用时间点还原时，你指定一个保持期。 存储帐户中的块 blob 可以在保持期内还原。

当你启用时间点还原时，保持期开始。 请记住，不能将 blob 还原到保持期开始之前的状态。 例如，如果在5月1日的保留期为30天时启用了时间点还原，则可以在5月15日还原到最多15天。 6月1日，你可以在1到30天之间还原数据。

时间点还原的保留期必须至少为一天，而不能早于为软删除指定的保持期。 例如，如果软删除保留期设置为7天，则时间点还原保留期可能介于1到6天之间。

### <a name="permissions-for-point-in-time-restore"></a>时间点还原的权限

若要启动还原操作，客户端必须对存储帐户中的所有容器具有写入权限。 若要使用 Azure Active Directory (Azure AD) 授予授权还原操作的权限，请将 " **存储帐户参与者** " 角色分配给存储帐户、资源组或订阅级别上的安全主体。

## <a name="limitations-and-known-issues"></a>限制和已知问题

块 blob 的时间点还原具有以下限制和已知问题：

- 只有标准常规用途 v2 存储帐户中的块 blob 可以作为时间点还原操作的一部分进行还原。 不会还原追加 blob、页 blob 和高级块 blob。 
- 如果在保留期内删除了某个容器，则该容器将不会随时间点还原操作一起还原。 如果尝试还原的 blob 范围包含已删除容器中的 blob，则时间点还原操作将失败。 若要了解如何防止删除容器，请参阅 [容器的软删除 (预览) ](soft-delete-container-overview.md)。
- 如果在当前时间与还原点之间的时间段内，blob 已移动到热层和冷层之间，则会将 blob 还原到其以前的层。 不支持在存档层中还原块 blob。 例如，如果热层中的某一 blob 在两天前已移至存档层，而还原操作还原至三天前的某个时间点，则该 blob 不会被还原至热层。 若要还原已存档的 blob，请先将其从存档层移开。 有关详细信息，请参阅[从存档层解冻 Blob 数据](storage-blob-rehydration.md)。
- 通过 [put](/rest/api/storageservices/put-block) 块或 [PUT 块从 URL](/rest/api/storageservices/put-block-from-url)上传的块，但不是通过 [put 块列表](/rest/api/storageservices/put-block-list)提交的块，它不是 blob 的一部分，因此不会在还原操作过程中还原。
- 无法还原具有活动租约的 blob。 如果要还原的 blob 范围包含具有活动租约的 blob，还原操作将以原子方式失败。 在启动还原操作之前，中断任何活动租约。
- 在还原操作过程中，不会创建或删除快照。 只有基本 blob 还原到其以前的状态。
- 不支持还原 Azure Data Lake Storage Gen2 平面和分层命名空间。

> [!IMPORTANT]
> 如果将块 blob 还原到早于2020年9月22日的点，则时间点还原的预览限制将有效。 Microsoft 建议你选择一个等于或晚于2020年9月22日的还原点，以利用一般可用的时间点还原功能。

## <a name="pricing-and-billing"></a>定价和计费

时间点还原的计费取决于为执行还原操作而处理的数据量。 处理的数据量取决于还原点和当前时间之间发生的更改数。 例如，如果存储帐户中的块 blob 数据的变化率相对较大，则还原操作将在1天后返回，恢复时间为10天。

若要估计还原操作的成本，请查看更改源日志，估计在还原期间修改的数据量。 例如，如果更改源的保留期为30天，并且更改源的大小为 10 MB，则还原到较早的10天，则在该区域中为 LRS 帐户列出的价格大约为三分之一。 如果还原到之前27天的点，则成本大约是所列价格的九个部分。

有关时点还原的定价的详细信息，请参阅 [阻止 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="next-steps"></a>后续步骤

- [对块 blob 数据执行时间点还原](point-in-time-restore-manage.md)
- [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)
- [为 blob 启用软删除](soft-delete-enable.md)
- [启用和管理 blob 版本控制](versioning-enable.md)
