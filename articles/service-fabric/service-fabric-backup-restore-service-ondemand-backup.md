---
title: Azure Service Fabric 中的按需备份 | Microsoft Docs
description: 使用 Service Fabric 的备份和还原功能来实现应用程序数据的按需备份。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730460"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric 中的按需备份

可将可靠有状态服务和 Reliable Actors 的数据进行备份，以解决灾难或数据丢失的情况。

Service Fabric 配备有[定期备份数据](service-fabric-backuprestoreservice-quickstart-azurecluster.md)和按需备份数据的功能。 按需备份很有用，因为它可防止由于在基础服务或其环境中的计划更改而导致的_数据丢失_/_数据损坏_。

在与服务或服务环境相关的任何手动触发的操作执行前，按需备份功能有助于捕获服务状态。 例如，更改服务二进制文件，也就是说，升级或降级服务，因为它将就应用程序代码中由 bug 导致的数据损坏进行数据保护。

## <a name="triggering-on-demand-backup"></a>触发按需备份

按需备份要求存储上载备份文件的详细信息。 按需备份将在定期备份策略中指定的存储或按需备份请求中指定的存储中进行。

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>对由定期备份策略指定的存储进行按需备份

可为可靠有状态服务或 Reliable Actor 的分区请求对定期备份策略中指定的存储进行额外的按需备份。 

以下情况是[启用可靠有状态服务和 Reliable Actors 的定期备份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所述的示例的延续，其中分区已启用备份策略并在 Azure 存储中按照所需的频率执行备份。

分区 ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的按需备份可由 [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API 触发。 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[按需备份进度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)可由 [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 跟踪。

### <a name="on-demand-backup-to-specified-storage"></a>指定存储的按需备份

可为可靠有状态服务或 Reliable Actor 的分区以及存储信息请求按需备份。 存储信息应被提供作为按需备份请求的一部分。

分区 ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的按需备份可由具有 Azure 存储信息的 [BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API 触发，如下所示。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[按需备份进度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)可由 [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 跟踪。


## <a name="tracking-on-demand-backup-progress"></a>跟踪按需备份进度

可靠有状态服务或 Reliable Actor 的分区一次仅接受一个按需备份请求。 仅当已完成当前的按需备份请求时，才能接受另一个请求。 

可以同时在不同分区上触发多个按需备份请求。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

按需备份请求的进度可能是以下状态之一

* **接受** - 已在分区启动备份且正在进行中。
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **成功/失败/超时** - 可在以下任一状态中完成请求的按需备份。 每种状态具有以下含义和响应详细信息。

    * **成功** - 备份状态为成功指示已成功备份分区状态。 响应将提供分区的 BackupEpoch 和 BackupLSN 以及 UTC 格式的时间。
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **失败** - 备份状态为失败指示在备份分区的状态期间出错。 将在响应中说明失败的原因。
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **超时** - 备份状态为超时指示在给定时间范围内无法创建分区状态备份；默认超时值为 10 分钟。 在此方案中建议在按需备份请求中启用大于 [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) 的新备份请求。

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>后续步骤
- [了解定期备份配置](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [备份还原 REST API 参考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

