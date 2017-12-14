---
title: "使用 Azure 资源管理器脚本管理 StorSimple 设备 | Microsoft Docs"
description: "了解如何使用 Azure 资源管理器脚本自动执行 StorSimple 作业"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: f4456200d6f497a87424f12a23034dbff00c75aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>使用基于 Azure 资源管理器 SDK 的脚本管理 StorSimple 设备

本文介绍了如何使用基于 Azure 资源管理器 SDK 的脚本，管理 StorSimple 8000 系列设备。 还通过示例脚本逐步展示了如何将环境配置为运行这些脚本。

本文仅适用于 Azure 门户中运行的 StorSimple 8000 系列设备。

## <a name="sample-scripts"></a>示例脚本

下面的示例脚本可用于自动执行各种 StorSimple 作业。

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>基于 Azure 资源管理器 SDK 的示例脚本表

| Azure 资源管理器脚本                    | 说明                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | 此脚本可用于授权 StorSimple 设备更改服务数据加密密钥。                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | 此脚本可用于创建 8010 或 8020 StorSimple 云设备。 然后，可以使用 StorSimple 数据管理器服务配置和注册云设备。                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | 此脚本可用于创建或修改 StorSimple 卷。                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | 此脚本可用于列出使用 StorSimple 设备管理器服务注册的设备的所有备份。                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | 此脚本可用于获取 StorSimple 设备的所有备份策略。                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | 此脚本可用于获取 StorSimple 设备管理器服务上运行的所有 StorSimple 作业。                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | 此脚本可用于扫描更新服务器，并在有更新可供 StorSimple 设备安装时发出通知。                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | 此脚本可用于在 StorSimple 设备上安装可用更新。                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | 此脚本可用于启动手动云快照，并删除超过指定保留天数的云快照。                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | 此 Azure 自动化 Runbook PowerShell 脚本可用于报告所有备份作业的状态。                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | 此脚本可用于删除一个备份对象。                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | 此脚本可用于在 StorSimple 设备上启动手动备份。                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | 此脚本可用于更新所有使用 StorSimple 设备管理器服务注册的 8010/8020 StorSimple 云设备的服务数据加密密钥。                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | 此脚本可用于在分析所有与备份策略关联的计划后突出显示缺少的备份。 还可以通过可用备份列表验证备份目录。             |




## <a name="configure-and-run-a-sample-script"></a>配置和运行示例脚本

此部分通过示例脚本详细介绍了运行脚本时需要执行的各种步骤。

### <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：

*   已安装 Azure PowerShell。 若要安装 Azure PowerShell 模块，请执行以下操作：
    * 在 Windows 环境中，按照[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) 中的步骤操作。 可以在 StorSimple 的 Windows Server 主机（如果使用）上安装 Azure PowerShell。
    * 在 Linux 或 MacOS 环境中，按照[在 MacOS 或 Linux 上安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurermps-maclinux?view=azurermps-4.4.0) 中的步骤操作。

若要详细了解如何使用 Azure PowerShell，请转到 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0)。

### <a name="run-azure-powershell-script"></a>运行 Azure PowerShell 脚本

此示例中使用的脚本可用于列出 StorSimple 设备上的所有作业。 这包括成功执行、无法执行或正在执行的作业。 请执行下面的步骤，下载并运行此脚本。

1. 启动 Azure PowerShell。 新建一个文件夹，并将目录更改为新文件夹。

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [将 NuGet CLI 下载](http://www.nuget.org/downloads)到上一步中创建的文件夹下。 nuget.exe 的版本有很多种。 选择与 SDK 对应的版本。 每个下载链接都直接指向 .exe 文件。 请务必右键单击文件，并将它保存到计算机，而不是在浏览器中运行它。

    也可以运行下面的命令，将此脚本下载并存储到先前创建的同一个文件夹中。
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. 下载依赖 SDK。

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. 从示例 GitHub 项目中下载此脚本。

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. 运行该脚本。 看到身份验证提示时，输入 Azure 凭据。 此脚本应输出 StorSimple 设备上所有作业的筛选列表。
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>示例输出

下面展示了运行此示例脚本时的输出。 输出包含注册设备上 2017 年 9 月 25 日开始并于 2017 年 10 月 2 日前完成的所有作业。

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>后续步骤

[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。