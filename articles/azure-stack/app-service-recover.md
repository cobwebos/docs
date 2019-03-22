---
title: 恢复 Azure Stack 上的应用服务 |Microsoft Docs
description: 有关 Azure Stack 应用服务灾难恢复的详细的指南
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340239"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>在 Azure Stack 上的应用服务的恢复

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

本文档提供有关操作的说明进行操作，需要针对应用服务灾难恢复。

若要从备份恢复 Azure Stack 上的应用服务，必须执行以下操作：
1.  还原应用服务数据库
2.  还原文件服务器共享内容
3.  还原应用服务角色和服务

如果 Azure Stack 存储用于函数应用存储，然后您还必须考虑还原函数应用的步骤。

## <a name="restore-the-app-service-databases"></a>还原应用服务数据库
应生产准备 SQL Server 实例上还原应用服务 SQL Server 数据库。 

之后[准备 SQL Server 实例](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance)来承载应用程序服务数据库，使用以下步骤从备份还原数据库：

1. 登录到将承载具有管理员权限已恢复的应用服务数据库的 SQL Server。
2. 使用以下命令从命令提示符下使用管理员权限运行还原应用服务数据库：
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. 验证这两个应用服务数据库已成功还原并退出 SQL Server Management Studio。

> [!NOTE]
> 若要从故障转移群集实例故障中恢复[这些说明](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017)。 

## <a name="restore-the-app-service-file-share-content"></a>还原应用服务文件共享内容
之后[准备文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)来托管应用服务文件共享，你需要从备份还原租户文件共享内容。 您可以使用任何方法可用于将文件复制到新创建的应用服务文件共享位置。 文件服务器上运行此示例将使用 PowerShell 和 robocopy 来连接到远程共享并将文件复制到共享：

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

除了复制文件共享内容，则必须重置本身的文件共享上的权限。 若要执行此操作，打开文件服务器计算机上的管理命令提示符并运行**ReACL.cmd**文件。 **ReACL.cmd**文件位于应用服务安装文件**BCDR**目录。

## <a name="restore-app-service-roles-and-services"></a>还原应用服务角色和服务
还原应用服务数据库和文件共享内容之后，接下来需要使用 PowerShell 来还原应用服务角色和服务。 这些步骤将还原应用服务的机密和服务配置。  

1. 登录到应用服务控制器**CN0-VM** VM 作为**roleadmin**使用在应用服务安装过程中提供的密码。 
    > [!TIP]
    > 你将需要修改 VM 的网络安全组，以便建立 RDP 连接。 
2. 复制**SystemSecrets.JSON**控制器 VM 的本地文件。 将需要提供对作为此文件的路径`$pathToExportedSecretFile`下一步中的参数。 
3. 在提升的 PowerShell 控制台窗口中使用以下命令还原应用服务角色和服务：

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> 强烈建议以关闭该命令完成后此 PowerShell 会话。

## <a name="restore-function-apps"></a>还原函数应用 
适用于 Azure Stack 的应用服务不支持还原租户用户应用程序或文件共享内容之外的数据。 因此，它们必须进行备份和恢复应用服务外部的备份和还原操作。 如果 Azure Stack 存储用于函数应用存储，则应执行以下步骤来恢复丢失的数据：

1. 创建新的存储帐户将由 Function App。 此存储可以是 Azure Stack 存储、 Azure 存储或任何兼容的存储。
2. 检索存储连接字符串。
3. 打开函数门户中，浏览到 function app。
4. 浏览到**平台功能**选项卡，单击**应用程序设置**。
5. 更改**AzureWebJobsDashboard**并**AzureWebJobsStorage**到新的连接字符串，然后单击**保存**。
6. 切换到**概述**。
7. 重新启动应用。 可能需要多次尝试清除所有错误。

## <a name="next-steps"></a>后续步骤
[Azure Stack 中的应用服务概述](azure-stack-app-service-overview.md)