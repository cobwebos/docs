---
title: 了解 Azure Service Fabric 中的定期备份配置 | Microsoft Docs
description: 使用 Service Fabric 的定期备份和还原功能来实现应用程序数据的定期数据备份。
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: 8cfa0e2a5aa1d7f560fe84f4eda18349f5d1d8b4
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992125"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>了解 Azure Service Fabric 中的定期备份配置

为可靠的有状态服务或 Reliable Actors 配置定期备份包括以下步骤：

1. **创建备份策略**：在此步骤中，根据需求创建一个或多个备份策略。

2. **启用备份**：在此步骤中，将在**步骤 1** 中创建的备份策略关联到所需的实体，即“应用程序”、“服务”或“分区”。

## <a name="create-backup-policy"></a>创建备份策略

备份策略包括以下配置：

* **在数据丢失时自动还原**：指定在分区发生数据丢失事件时是否使用最新的可用备份自动触发还原。

* **最大增量备份数**：定义在两个完整备份之间可执行的增量备份的最大数目。 最大增量备份数指定上限。 在下列条件之一下，可以在完成指定数目的增量备份前执行完整备份

    1. 副本在变为主副本后从未执行过完整备份。

    2. 自上次备份以来的一些日志记录已截断。

    3. 副本超出了 MaxAccumulatedBackupLogSizeInMB 限制。

* **备份计划**：要执行定期备份的时间或频率。 可以将备份安排为按指定的时间间隔或者每日/每周在固定的时间进行。

    1. **基于频率的备份计划**：如果需要按固定时间间隔执行数据备份，应使用此计划类型。 两个连续备份之间的所需时间间隔是使用 ISO8601 格式定义的。 基于频率的备份计划支持的最高时间间隔分辨率为分钟。
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **基于时间的备份计划**：如果需要在每天或每周的特定时间执行数据备份，应使用此计划类型。 计划频率类型可以是每日或每周。
        1. **“每日”基于时间的备份计划**：如果需要在每天的特定时间执行数据备份，应使用此计划类型。 若要指定此计划类型，请将 `ScheduleFrequencyType` 设置为 _Daily_，将 `RunTimes` 以 ISO8601 格式设置为每天中的所需时间的列表，随时间一起指定的日期将被忽略。 例如，`0001-01-01T18:00:00` 表示每天下午 6:00，忽略日期部分 _0001-01-01_。 下面的示例展示了在每天上午 9:00 和下午 6:00  点触发每日备份的配置。

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **“每周”基于时间的备份计划**：如果需要在某天的特定时间执行数据备份，应使用此计划类型。 若要指定此计划类型，请将 `ScheduleFrequencyType` 设置为 _Weekly_，将 `RunDays` 设置为需要触发备份的星期几的列表，将 `RunTimes` 以 ISO8601 格式设置为每天中的所需时间的列表，随时间一起指定的日期将被忽略。 要触发定期备份的星期几的列表。 下面的示例展示了从星期一到星期五在上午 9:00 和下午 6:00  点触发每日备份的配置。

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **备份存储**：指定要将备份上传到的位置。 存储可以是 Azure Blob 存储或文件共享。
    1. **Azure blob 存储**：需要将生成的备份存储在 Azure 中时，应选择此存储类型。 “独立的”和“基于 Azure 的”群集都可以使用此存储类型。 描述此存储类型需要提供连接字符串和要将备份上传到的容器的名称。 如果具有指定名称的容器不可用，则在上传备份期间会创建该容器。
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **文件共享**：当需要将数据备份存储在本地时，应当为“独立的”群集选择此存储类型。 描述此存储类型需要提供要将备份上传到的文件共享路径。 可以使用以下选项之一配置对文件共享的访问权限
        1. _集成 Windows 身份验证_，这会将对文件共享的访问权限提供给属于 Service Fabric 群集的所有计算机。 在这种情况下，请设置以下字段来配置基于“文件共享”的备份存储。

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _使用用户名和密码保护文件共享_，这会将对文件共享的访问权限提供给特定用户。 在指定文件共享存储时还可以指定辅助用户名和辅助密码来提供回退凭据，以防使用主用户名和主密码进行身份验证失败。 在这种情况下，请设置以下字段来配置基于“文件共享”的备份存储。
            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> 请确保存储可靠性满足或高于备份数据的可靠性要求。
>

## <a name="enable-periodic-backup"></a>启用定期备份
在定义备份策略来满足数据备份要求后，应当将备份策略与“应用程序”、“服务”或“分区”相关联。

### <a name="hierarchical-propagation-of-backup-policy"></a>备份策略的分层传播
在 Service Fabric 中，应用程序、服务和分区之间的关系是分层的，如[应用程序模型](./service-fabric-application-model.md)中所述。 备份策略可以与层次结构中的“应用程序”、“服务”或“分区”相关联。 备份策略将按层次结构传播到下一级别。 假设仅创建了一个备份策略并将其与某个“应用程序”相关联，则所有属于该应用程序的所有“可靠有状态服务”和 _Reliable Actors_ 的有状态分区都将使用该备份策略进行备份。 或者，如果该备份策略与某个“可靠有状态服务”相关联，则其所有分区都将使用该备份策略进行备份。

### <a name="overriding-backup-policy"></a>替代备份策略
某些情况下，应用程序的大多数服务的数据备份要求使用相同的备份计划，但某些服务除外，这些服务要求数据备份使用频率更高的计划或者要求将备份存放到不同的存储帐户或文件共享。 为应对这样的情况，备份还原服务提供了相关机制来用于在服务和分区作用域内替代传播的策略。 当备份策略与“服务”或“分区”相关联时，它将替代所传播的任何备份策略。

### <a name="example"></a>示例

此示例将设置用于两个应用程序：_MyApp_A_ 和 _MyApp_B_。 应用程序 _MyApp_A_ 包含两个可靠有状态服务 _SvcA1_ & _SvcA3_ 和一个 Reliable Actors 服务 _ActorA2_。 _SvcA1_ 包含三个分区，而 _ActorA2_ 和 _SvcA3_ 各包含两个分区。  应用程序 _MyApp_B_ 包含三个可靠有状态服务 _SvcB1_、_SvcB2_ 和 _SvcB3_。 _SvcB1_ 和 _SvcB2_ 各包含两个分区，而 _SvcB3_ 包含三个分区。

假设这些应用程序的数据备份要求如下所述

1. MyApp_A
    1. 为属于应用程序的所有“可靠有状态服务”和 _Reliable Actors_ 的所有分区创建数据的每日备份。 将备份数据上传到位置 _BackupStore1_。

    2. 其中一个服务 _SvcA3_ 要求每小时进行数据备份。

    3. 分区 _SvcA1_P2_ 中的数据大小高于预期值，应当将其备份数据存储到另一个存储位置 _BackupStore2_。

2. MyApp_B
    1. 在每星期日的早上 8:00 为 _SvcB1_ 服务的所有分区创建数据备份。 将备份数据上传到位置 _BackupStore1_。

    2. 在每天的早上 8:00 为分区 _SvcB2_P1_ 创建数据备份。 将备份数据上传到位置 _BackupStore1_。

为解决这些数据备份要求，将创建备份策略 BP_1 到 BP_5 并启用备份，如下所述。
1. MyApp_A
    1. 创建备份策略 _BP_1_，使其采用基于频率的备份计划且将频率设置为 24 小时。 将备份存储配置为使用存储位置 _BackupStore1_。 使用[启用应用程序备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API 为应用程序 _MyApp_A_ 启用此策略. 此操作为属于应用程序 _MyApp_A_ 的“可靠有状态服务”和 _Reliable Actors_ 的所有分区启用使用备份策略 _BP_1_ 的数据备份。

    2. 创建备份策略 _BP_2_，使其采用基于频率的备份计划且将频率设置为 1 小时。 将备份存储配置为使用存储位置 _BackupStore1_。 使用[启用服务备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableservicebackup) API 为服务 _SvcA3_ 启用此策略。 此操作将使用显式启用的备份策略 _BP_2_ 为服务 _SvcA3_ 的所有分区替代传播的策略 _BP_1_，从而导致使用备份策略 _BP_2_ 为这些分区执行数据备份。

    3. 创建备份策略 _BP_3_，使其采用基于频率的备份计划且将频率设置为 24 小时。 将备份存储配置为使用存储位置 _BackupStore2_。 使用[启用分区备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API 为分区 _SvcA1_P2_ 启用此策略。 此操作将使用显式启用的备份策略 _BP_3_ 为分区 _SvcA1_P2_ 替代传播的策略 _BP_1_。

2. MyApp_B
    1. 创建备份策略 _BP_4_，使其采用基于时间的备份计划，将计划频率类型设置为每周，将运行日设置为星期日，将运行时间设置为早上 8:00。 将备份存储配置为使用存储位置 _BackupStore1_。 使用[启用服务备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableservicebackup) API 为服务 _SvcB1_ 启用此策略。 此操作为服务 _SvcB1_ 的所有分区启用使用备份策略 _BP_4_ 的数据备份。

    2. 创建备份策略 _BP_5_，使其采用基于时间的备份计划，将计划频率类型设置为每日，将运行时间设置为早上 8:00。 将备份存储配置为使用存储位置 _BackupStore1_。 使用[启用分区备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API 为分区 _SvcB2_P1_ 启用此策略。 此操作为分区 _SvcB2_P1_ 启用使用备份策略 _BP_5_ 的数据备份。

下图描绘了显式启用的备份策略和传播的备份策略。

![Service Fabric 应用程序层次结构][0]

## <a name="disable-backup"></a>禁用备份
不需要对数据进行备份时，可以禁用备份策略。 在“应用程序”上启用的备份策略只能在同一“应用程序”上使用[禁用应用程序备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API 进行禁用，在“服务”上启用的备份策略可以在同一“服务”上使用[禁用服务备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disableservicebackup) API 进行禁用，在“分区”上启用的备份策略可以在同一“分区”上使用[禁用分区备份](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API 进行禁用。

* 为“应用程序”禁用备份策略将停止因为传播到可靠有状态分区或 Reliable Actors 分区的备份策略而发生的所有定期数据备份。

* 为“服务”禁用备份策略将停止因为传播到“服务”的分区的此备份策略而发生的所有定期数据备份。

* 为“分区”禁用备份策略将停止因为分区处的备份策略而发生的所有定期数据备份。

## <a name="suspend--resume-backup"></a>暂停和恢复备份
某些情况下可能需要临时暂停定期数据备份。 在这种情况下，可以根据需要在“应用程序”、“服务”或“分区”上使用“暂停备份”API。 定期备份暂停将在应用程序层次结构的子树中从应用暂停的点开始向下传递。 

* 当在某个“应用程序”上使用[暂停应用程序备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API 应用暂停时，此应用程序下的所有服务和分区都将暂停定期数据备份。

* 当在某个“服务”上使用[暂停服务备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendservicebackup) API 应用暂停时，此服务下的所有分区都将暂停定期数据备份。

* 当在某个“分区”上使用[暂停分区备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API 应用暂停时，此分区将暂停定期数据备份。

不再需要暂停时，可以使用各自的恢复备份 API 来恢复定期数据备份。 必须在暂停定期备份时所在的同一“应用程序”、“服务”或“分区”上进行恢复。

* 如果暂停是在“应用程序”上应用的，则应当使用[恢复应用程序备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API 进行恢复。 

* 如果暂停是在“服务”上应用的，则应当使用[恢复服务备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumeservicebackup) API 进行恢复。

* 如果暂停是在“分区”上应用的，则应当使用[恢复分区备份](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API 进行恢复。

## <a name="auto-restore-on-data-loss"></a>在数据丢失时自动还原
服务分区可能会由于意外故障而导致数据丢失。 例如，分区的三个副本中的两个（包括主副本）的磁盘数据已损坏或被擦除。

当 Service Fabric 检测到分区丢失了数据时，它会对分区调用 `OnDataLossAsync` 接口方法并期望分区采取所需的操作来避免数据丢失。 在这种情况下，如果在分区上生效的备份策略将 `AutoRestoreOnDataLoss` 标志设置为 `true`，则将自动触发还原并使用此分区的最新可用备份。

## <a name="get-backup-configuration"></a>获取备份配置
有单独的 API 可用来在“应用程序”、“服务”和“分区”作用域内获取备份配置信息。 这些 API 分别是[获取应用程序备份配置信息](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)、[获取服务备份配置信息](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)和[获取分区备份配置信息](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo)。 这些 API 主要返回适用的备份策略、备份策略应用于的作用域以及备份暂停详细信息。 下面是有关这些 API 的返回结果的简要说明。

- 应用程序备份配置信息：提供在应用程序上应用的备份策略的详细信息，以及在属于该应用程序的服务和分区上被替代的所有策略。 它还包括应用程序及其服务和分区的暂停信息。

- 服务备份配置信息：提供在服务上生效的备份策略的详细信息，此策略应用于的作用域，以及其分区上被替代的所有策略。 它还包括服务及其分区的暂停信息。

- 分区备份配置信息：提供在分区上生效的备份策略的详细信息，以及此策略应用于的作用域。 它还包括分区的暂停信息。

## <a name="list-available-backups"></a>列出可用备份

可以使用“获取备份列表”API 列出可用备份。 此 API 调用的结果包括与备份存储上可用的所有备份相关的备份信息项，备份存储是在适用的备份策略中配置的。 提供了此 API 的不同变体，用以列出属于应用程序、服务或分区的可用备份。 这些 API 支持获取所有适用的分区的“最新”可用备份，也支持根据“开始日期”和“结束日期”来筛选备份。

这些 API 还支持对结果进行分页，当 _MaxResults_ 参数设置为非零正整数时，API 将返回最多 _MaxResults_ 个备份信息项。 如果可用的备份信息项多于 _MaxResults_ 值，则会返回一个继续标记。 可以使用有效的继续标记参数来获取下一组结果。 将有效的继续标记值传递给下一次 API 调用时，该 API 将返回下一组结果。 当返回了所有可用结果时，响应中将不包含继续标记。

下面是有关受支持变体的简要信息。

- [获取应用程序备份列表](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)：返回属于给定 Service Fabric 应用程序的每个分区的可用备份的列表。

- [获取服务备份列表](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getservicebackuplist)：返回属于给定 Service Fabric 服务的每个分区的可用备份的列表。
 
- [获取分区备份列表](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getpartitionbackuplist)：返回指定分区的可用备份的列表。

## <a name="next-steps"></a>后续步骤
- [备份还原 REST API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png