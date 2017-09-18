---
title: "使用 PowerShell 备份和还原应用服务应用"
description: "了解如何使用 PowerShell 在 Azure 应用服务中备份和还原应用"
services: app-service
documentationcenter: 
author: NKing92
manager: erikre
editor: 
ms.assetid: 7ea8661e-aefb-4823-9626-6bff980cdebf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 6866a16e40ea7b6953b13ae72e207f3f69a3030c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>使用 PowerShell 备份和还原应用服务应用

了解如何使用 Azure PowerShell 备份和还原[应用服务应用](https://azure.microsoft.com/services/app-service/web/)。 有关 Web 应用备份的详细信息（包括要求和限制），请参阅[在 Azure 应用服务中备份 Web 应用](../app-service-web/web-sites-backup.md)。

## <a name="prerequisites"></a>先决条件
若要使用 PowerShell 管理应用备份，需满足以下条件：

* **一个 SAS URL**，允许读写访问 Azure 存储容器。 有关 SAS URL 的说明，请参阅[了解 SAS 模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 有关使用 PowerShell 管理 Azure 存储的示例，请参阅[结合使用 Azure PowerShell 与 Azure 存储](../storage/common/storage-powershell-guide-full.md)。
* **一个数据库连接字符串**，用于备份数据库和 Web 应用。

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>如何生成可用于 Web 应用备份 cmdlet 的 SAS URL
可以通过 PowerShell 生成 SAS URL。 下面是一个示例，说明如何生成一个可用于本文所述 cmdlet 的 SAS URL。

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>安装 Azure PowerShell 1.3.2 或更高版本
有关安装并使用 Azure PowerShell 的说明，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](/powershell/azure/overview)。

## <a name="create-a-backup"></a>创建备份
使用 New-AzureRmWebAppBackup cmdlet 创建 Web 应用的备份。

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

此方法使用自动生成的名称创建备份。 若要为备份提供一个名称，请使用 BackupName 可选参数。

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

要在备份中包括数据库，请首先使用 New-AzureRmWebAppDatabaseBackupSetting cmdlet 创建数据库备份设置，然后在 New-AzureRmWebAppBackup cmdlet 的 Databases 参数中提供该设置。 Databases 参数接受一组数据库设置，允许用户备份多个数据库。

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>获取备份
Get-AzureRmWebAppBackupList cmdlet 返回一个数组，其中包括 Web 应用的所有备份。 必须提供 Web 应用的名称及其资源组。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

若要获取特定备份，可使用 Get-AzureRmWebAppBackup cmdlet。

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

为方便起见，也可将 Web 应用对象通过管道发送到任意备份管理 cmdlet。

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>计划自动备份
可以对备份进行计划，使之按指定的时间间隔自动发生。 若要配置备份计划，可使用 Edit-AzureRmWebAppBackupConfiguration cmdlet。 该 cmdlet 使用多个参数：

* **Name** - Web 应用的名称。
* **ResourceGroupName** - 内含 Web 应用的资源组的名称。
* **Slot** - 可选。 Web 应用槽的名称。
* **StorageAccountUrl** - 用于存储备份的 Azure 存储容器的 SAS URL。
* **FrequencyInterval** - 一个数字值，表示进行备份的频率。 必须是正整数。
* **FrequencyUnit** - 时间单位，表示进行备份的频率。 选项为“小时”和“天”。
* **RetentionPeriodInDays** - 自动备份在被自动删除之前应保存的天数。
* **StartTime** - 可选。 自动备份开始的时间。 如果此字段为 null，则立即开始备份。 必须为日期/时间。
* **Databases** - 可选。 一组 DatabaseBackupSettings，适用于要备份的数据库。
* **KeepAtLeastOneBackup** - 可选开关参数。 如果需要始终在存储帐户中保留一个备份，不管该备份有多老，则请提供此项。

下面是此 cmdlet 的用法示例。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

若要获取当前备份计划，可使用 Get-AzureRmWebAppBackupConfiguration cmdlet。 此项可用于修改已配置的计划。

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>从备份中还原 Web 应用
若要从备份中还原 Web 应用，可使用 Restore-AzureRmWebAppBackup cmdlet。 若要使用此 cmdlet，最简单的方式是通过管道输入从 Get-AzureRmWebAppBackup cmdlet 或 Get-AzureRmWebAppBackupList cmdlet 检索的备份对象。

有了备份对象以后，即可通过管道将其输入 Restore-AzureRmWebAppBackup cmdlet。 指定 Overwrite 开关参数，指示想要使用备份的内容覆盖 Web 应用的内容。 如果备份中包含数据库，则也会还原这些数据库。

        $backup | Restore-AzureRmWebAppBackup -Overwrite

下面是一个指定了所有参数的示例，说明了如何使用 Restore-AzureRmWebAppBackup。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>删除备份
若要删除备份，可使用 Remove-AzureRmWebAppBackup cmdlet。 此项从存储帐户删除备份。 指定应用名称和其资源组，以及要删除的备份的 ID。

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

也可通过管道方式将备份对象输入 Remove-AzureRmWebAppBackup cmdlet 中进行删除。

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite

