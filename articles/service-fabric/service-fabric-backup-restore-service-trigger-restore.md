---
title: 在 Azure Service Fabric 中还原备份 | Microsoft Docs
description: 使用 Service Fabric 的定期备份和还原功能来从应用程序数据的备份还原数据。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730465"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>在 Azure Service Fabric 中还原备份


Service Fabric 中的可靠有状态服务和 Reliable Actors 可在请求和响应或完整的事务之外维持可变的权威状态。 如果有状态服务长时间不可用或由于灾难而丢失信息，可能需要还原到其状态的最近可接受备份，以便在其备份后继续提供服务。

例如，服务可能要备份其数据，以防止出现以下情况：

- 整个 Service Fabric 群集永久丢失。 **（灾难恢复的情况 - DR）**
- 大部分服务分区副本永久丢失。 **（数据丢失的情况）**
- 状态被意外删除或受损而引起管理错误。 例如，具有足够权限的管理员错误地删除了服务。（数据丢失的情况）
- 服务中的 bug 导致数据损坏。 例如，当某个服务代码升级程序开始将错误数据写入到可靠集合中时可能发生数据损坏。 在此情况下，代码和数据可能都必须还原到先前的状态。 **（数据损坏的情况）**


## <a name="prerequisites"></a>先决条件
* 若要触发，还原应为群集启用的故障分析服务 (FAS)
* 要还原的备份应通过备份还原服务 (BRS) 执行
* 只能在分区触发还原。

## <a name="triggering-restore"></a>触发还原

还原可用于以下任何情况 
* 发生灾难恢复 (DR) 时进行数据还原
* 发生数据损坏/数据丢失时进行数据还原



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>发生灾难恢复 (DR) 时进行数据还原
丢失整个 Service Fabric 群集时，可靠有状态服务和 Reliable Actors 的分区数据可以还原到备用群集。 可以从[具有备份存储详细信息的 GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) 的枚举中选择所需的备份。 枚举备份可用于应用程序、服务或分区。

假设丢失的群集是[启用可靠有状态服务和 Reliable Actors 的定期备份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所述的已部署 `SampleApp` 的群集，其中分区已启用备份策略并在 Azure 存储中进行备份。 


执行以下 PowerShell 脚本，调用 REST API 来枚举为丢失的 Service Fabric 群集中的 `SampleApp` 应用程序内所有分区创建的备份。 枚举 API 需要存储信息，其中将存储应用程序的备份，用于枚举可用备份。 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
上述运行的示例输出：

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



若要触发还原，我们需要选择所需的备份。 将以下备份用作当前灾难恢复 (DR) 所需的备份

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

对于还原 API，我们需要提供 BackupId 和 BackupLocation 详细信息。 需要根据[分区方案](service-fabric-concepts-partitioning.md#get-started-with-partitioning)选择备用群集中的分区。 用户负责根据原始丢失群集中的分区方案选择要从备用群集还原备份的目标分区。

假设备用群集上的分区 ID 为 `1c42c47f-439e-4e09-98b9-88b8f60800c6`，该分区 ID 会通过比较范围分区 (UniformInt64Partition) 的高键值和低键值来映射到原始群集分区 ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22`。

对于已命名分区，将比较名称值以识别备用群集中的目标分区。

通过以下[还原 API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) 根据备份群集的分区请求还原

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
还原的进度可以是 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>发生数据损坏/数据丢失时进行数据还原

发生数据丢失或数据损坏时，可靠有状态服务和 Reliable Actors 的分区数据可以还原到任何所选备份。 以下情况是[启用可靠有状态服务和 Reliable Actors 的定期备份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所述的示例的延续，其中分区已启用备份策略并在 Azure 存储中按照所需的频率执行备份。 

将从 [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) 的输出中选择所需的备份。 在这种情况下，将从过去的同一个群集中生成备份。
若要触发还原，我们需要从列表中选择所需的备份。 将以下备份用作当前数据丢失 / 数据损坏所需的备份

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

对于还原 API，我们需要提供 BackupId 和 BackupLocation 详细信息。 由于群集已启用备份，因此 Service Fabric 备份还原服务 (BRS) 可识别出关联备份策略中的正确存储位置。

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

还原的进度可以是 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>跟踪还原进度

可靠有状态服务或 Reliable Actor 的分区一次仅接受一个还原请求。 仅当已完成当前的还原请求时，才能接受另一个请求。 可以同时在不同分区上触发多个还原请求。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

按以下顺序显示还原请求进度

1. __已接受__ - 还原状态为“已接受”表示已使用正确的请求参数触发请求。
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __正在进行__ - 还原状态为“正在进行”表示分区将使用请求中所述的备份进行还原。 分区将报告“数据丢失”状态。
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __成功__/ __失败__/ __超时__ - 可在以下任何状态下完成请求的还原。 每种状态具有以下含义和响应详细信息。
       
    * __成功__ - 还原状态为“成功”表示已重新获取分区状态。 响应将提供分区的 RestoreEpoch 和 RestordLSN 以及 UTC 格式的时间。 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __失败__ - 还原状态为“失败”表示还原请求失败。 将在请求中说明失败的原因。
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __超时__ - 还原状态为“超时”表示请求已超时。 建议新的还原请求使用更大的 [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout)；默认超时为 10 分钟。 建议在再次请求还原之前确保分区不处于数据丢失状态。
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>自动还原

可以为自动还原配置 Service Fabric 群集中可靠有状态服务和 Reliable Actors 的分区。 创建备份策略时，该策略可以将 `AutoRestore` 设置为 true。  对分区启用自动还原可在报告数据丢失时从最新备份还原数据。
 
 [备份策略中的自动还原支持](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>后续步骤
- [了解定期备份配置](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [备份还原 REST API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
