---
title: 排查 Azure 文件共享备份问题
description: 本文提供在保护 Azure 文件共享时所发生的问题的故障排除信息。
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: a9b3514b4c1a00cc2f9bb1e1922975bf0bb70d24
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562077"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>在备份 Azure 文件共享时排查问题

本文提供了有关使用 Azure 备份服务配置备份或还原 Azure 文件共享时遇到的任何问题的疑难解答信息。

## <a name="common-configuration-issues"></a>常见配置问题

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>找不到存储帐户，无法为 Azure 文件共享配置备份

- 等到发现完成。
- 检查存储帐户下的任何文件共享是否已由另一个恢复服务保管库保护。

  >[!NOTE]
  >一个存储帐户中的所有文件共享只能在一个恢复服务保管库中进行保护。 您可以使用[此脚本](scripts/backup-powershell-script-find-recovery-services-vault.md)查找您的存储帐户注册到的恢复服务保管库。

- 确保文件共享不在任何不受支持的存储帐户中。 可以参考[Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)来查找支持的存储帐户。
- 检查存储帐户的防火墙设置，以确保启用 "允许受信任的 Microsoft 服务访问存储帐户" 选项。

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>门户中的错误指出无法发现存储帐户

如果有合作伙伴订阅（已启用 CSP），请忽略此错误。 如果订阅未启用 CSP，并且无法发现存储帐户，请联系支持人员。

### <a name="selected-storage-account-validation-or-registration-failed"></a>选择的存储帐户验证或注册失败

重试注册。 如果问题仍然存在，请联系支持部门。

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>无法在所选存储帐户中列出或查找文件共享

- 请确保存储帐户存在于资源组中，但在保管库中的上次验证或注册后尚未删除或移动。
- 确保未删除你要保护的文件共享。
- 请确保存储帐户是支持进行文件共享备份的存储帐户。 可以参考[Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)来查找支持的存储帐户。
- 检查是否已在同一恢复服务保管库中对文件共享进行保护。

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>备份文件共享配置（或保护策略配置）失败

- 请重试该配置，以确定问题是否仍然存在。
- 确保尚未删除要保护的文件共享。
- 如果你尝试同时保护多个文件共享，并且某些文件共享失败，请尝试再次为失败的文件共享配置备份。

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>取消保护文件共享后无法删除恢复服务保管库

在 Azure 门户中，打开**保管库** > **备份基础结构** > **存储帐户**，然后单击 "**注销**" 以从恢复服务保管库中删除存储帐户。

>[!NOTE]
>只有注销已注册到保管库的所有存储帐户后，才能删除恢复服务保管库。

## <a name="common-backup-or-restore-errors"></a>常见的备份或还原错误

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound-操作失败，因为找不到文件共享

错误代码： FileShareNotFound

错误消息：操作失败，因为找不到文件共享

确保未删除你要尝试保护的文件共享。

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-找不到或不支持存储帐户

错误代码： UserErrorFileShareEndpointUnreachable

错误消息：找不到或不支持存储帐户

- 请确保存储帐户存在于资源组中，但未在上次验证后从资源组中删除或删除。

- 请确保存储帐户是支持进行文件共享备份的存储帐户。

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-已达到此文件共享的快照的最大限制;你将能够更好地使用较旧的过期时间

错误代码： AFSMaxSnapshotReached

错误消息：已达到此文件共享的快照的最大限制;更早的时间到期后，你将能够执行更多的工作。

- 为文件共享创建多个按需备份时，可能出现此错误。
- 每个文件共享的快照数限制为200，包括 Azure 备份占用的快照数。 较旧的计划备份（或快照）会自动清除。 如果达到最大限制，则必须删除按需备份（或快照）。

从 Azure 文件门户删除按需备份（Azure 文件共享快照）。

>[!NOTE]
> 如果删除 Azure 备份创建的快照，会失去恢复点。

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-操作失败，因为指定的存储帐户不再存在

错误代码： UserErrorStorageAccountNotFound

错误消息：操作失败，因为指定的存储帐户不再存在。

确保存储帐户仍然存在且不会被删除。

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-提供的存储帐户详细信息不正确

错误代码： UserErrorDTSStorageAccountNotFound

错误消息：提供的存储帐户详细信息不正确。

确保存储帐户仍然存在且不会被删除。

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-资源组不存在

错误代码： UserErrorResourceGroupNotFound

错误消息：资源组不存在

选择现有资源组或创建新资源组。

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-此文件共享的备份作业已在进行中

错误代码： ParallelSnapshotRequest

错误消息：此文件共享的备份作业已在进行中。

- 文件共享备份不支持针对同一文件共享的并行快照请求。

- 请等待现有备份作业完成，然后重试。 如果在恢复服务保管库中找不到备份作业，请检查同一订阅中的其他恢复服务保管库。

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-文件共享备份或还原由于存储服务限制而失败。 这可能是因为存储服务正忙于处理给定存储帐户的其他请求

错误代码： FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

错误消息：文件共享备份或还原由于存储服务限制而失败。 这可能是因为存储服务正忙于处理给定存储帐户的其他请求。

稍后尝试备份/还原操作。

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-找不到目标文件共享

错误代码： TargetFileShareNotFound

错误消息：找不到目标文件共享。

- 请确保所选存储帐户存在，且目标文件共享未删除。

- 请确保存储帐户是支持进行文件共享备份的存储帐户。

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-备份或还原作业失败，因为存储帐户处于锁定状态

错误代码： UserErrorStorageAccountIsLocked

错误消息：由于存储帐户处于锁定状态，备份或还原作业失败。

请删除对存储帐户的锁定，或者使用**删除锁定**而不是**读取锁定**，然后重试备份或还原操作。

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached-恢复失败，因为失败的文件数超过了阈值

错误代码： DataTransferServiceCoFLimitReached

错误消息：恢复失败，因为失败的文件数超过了阈值。

- 恢复失败原因列在文件（作业详细信息中提供的路径）中。 解决失败，并仅对失败的文件重试还原操作。

- 文件还原失败的常见原因：

  - 已失败的文件当前正在使用中
  - 父目录中存在其名称与故障文件名称相同的目录。

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-恢复失败，因为无法恢复任何文件

错误代码： DataTransferServiceAllFilesFailedToRecover

错误消息：恢复失败，因为无法恢复任何文件。

- 恢复失败原因列在文件（作业详细信息中提供的路径）中。 解决导致失败的问题，然后只对故障文件重试还原操作。

- 文件还原失败的常见原因：

  - 已失败的文件当前正在使用中
  - 父目录中存在其名称与故障文件名称相同的目录。

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid-还原失败，因为源中的一个文件不存在

错误代码： DataTransferServiceSourceUriNotValid

错误消息：还原失败，因为源中的一个文件不存在。

- 所选项不在恢复点数据中。 若要恢复这些文件，请提供正确的文件列表。
- 与恢复点对应的文件共享快照已手动删除。 请选择另一恢复点，然后重试还原操作。

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked-恢复作业正在处理同一目标

错误代码： UserErrorDTSDestLocked

错误消息：恢复作业正在处理同一目标。

- 文件共享备份不支持对同一目标文件共享的并行恢复。

- 等待现有恢复完成，然后再试一次。 如果在恢复服务保管库中找不到恢复作业，请检查同一订阅中的其他恢复服务保管库。

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull-还原操作失败，因为目标文件共享已满

错误代码： UserErrorTargetFileShareFull

错误消息：还原操作失败，因为目标文件共享已满。

请增加目标文件共享大小配额以容纳还原数据，然后重试还原操作。

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-目标文件共享的存储大小配额不足，无法还原

错误代码： UserErrorTargetFileShareQuotaNotSufficient

错误消息：目标文件共享的存储大小配额不足，无法还原

请增加目标文件共享大小配额以容纳还原数据，然后重试该操作

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>文件同步 PreRestoreFailed-还原操作失败，因为在对与目标文件共享相关联的文件同步服务资源执行预还原操作时出错

错误代码：文件同步 PreRestoreFailed

错误消息：还原操作失败，因为在对与目标文件共享相关联文件同步服务资源执行预还原操作时出错。

请稍后尝试还原数据。 如果问题持续出现，请联系 Microsoft 支持。

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-正在为目标文件共享 Azure 文件同步服务更改检测。 更改检测由上一次还原到目标文件共享触发

错误代码： AzureFileSyncChangeDetectionInProgress

错误消息：目标文件共享正在进行 Azure 文件同步服务更改检测。 更改检测由上一次还原到目标文件共享触发。

使用其他目标文件共享。 或者，你可以等待为目标文件共享完成 Azure 文件同步服务更改检测，然后重试还原。

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored-一个或多个文件无法成功恢复。 有关详细信息，请查看以上给定路径中的失败文件列表

错误代码： UserErrorAFSRecoverySomeFilesNotRestored

错误消息：一个或多个文件无法成功恢复。 有关详细信息，请查看上面给出的路径中的故障文件列表。

- 恢复失败原因列在文件（作业详细信息中提供的路径）中。 解决原因，并重试仅对失败的文件执行还原操作。
- 文件还原失败的常见原因：

  - 已失败的文件当前正在使用中
  - 父目录中存在其名称与故障文件名称相同的目录。

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-找不到与恢复点相对应的 Azure 文件共享快照

错误代码： UserErrorAFSSourceSnapshotNotFound

错误消息：找不到与恢复点相对应的 Azure 文件共享快照

- 确保文件共享快照与你尝试用于恢复的恢复点相对应，仍然存在。

  >[!NOTE]
  >如果删除由 Azure 备份创建的文件共享快照，则相应的恢复点将变为不可用。 建议不要删除快照以确保恢复。

- 请尝试选择另一个还原点以恢复数据。

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-正在同一目标文件共享上执行另一个还原作业

错误代码： UserErrorAnotherRestoreInProgressOnSameTarget

错误消息：正在同一目标文件共享上正在进行另一个还原作业

使用其他目标文件共享。 或者，你可以取消或等待其他还原完成。

## <a name="common-modify-policy-errors"></a>常见的修改策略错误

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-正在对此项目进行另一配置保护操作

错误代码： BMSUserErrorConflictingProtectionOperation

错误消息：正在对此项目进行另一配置保护操作。

等待上一个修改策略操作完成，稍后重试。

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked-所选项目正在进行另一个操作

错误代码： BMSUserErrorObjectLocked

错误消息：正在对所选项执行其他操作。

等待其他正在进行的操作完成，稍后重试。

## <a name="next-steps"></a>后续步骤

有关备份 Azure 文件共享的详细信息，请参阅：

- [备份 Azure 文件共享](backup-afs.md)
- [备份 Azure 文件共享常见问题](backup-azure-files-faq.md)
