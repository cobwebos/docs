---
title: 备份 Azure Stack 上的应用服务 |Microsoft Docs
description: Azure Stack 应用服务备份的详细的指南。
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
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340546"
---
# <a name="back-up-app-service-on-azure-stack"></a>备份 Azure Stack 上的应用服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

本文档提供有关如何备份 Azure Stack 上的应用服务的说明。

> [!IMPORTANT]
> Azure Stack 上的应用服务不会备份作为的一部分[Azure Stack 基础结构备份](azure-stack-backup-infrastructure-backup.md)。 Azure Stack 操作员，则必须采取措施以确保应用服务可以成功地恢复必要。

Azure Stack 上的 azure 应用服务具有用于灾难恢复规划时要考虑的四个主要组件：
1. 资源提供程序基础结构使用;服务器角色、 辅助角色层等。 
2. 应用服务密钥
3. 应用服务的 SQL Server 托管和计量数据库
4. 应用服务的用户工作负载内容存储在应用服务的文件共享   

## <a name="back-up-app-service-secrets"></a>备份应用服务的机密
当从备份中恢复应用服务，需要提供使用初始部署的应用程序服务项。 此信息应保存在应用服务是已成功部署并存储在安全位置。 在使用应用服务恢复 PowerShell cmdlet 恢复期间，将从备份创建资源提供程序基础结构配置。

使用管理门户以备份应用程序服务密钥通过执行以下步骤： 

1. 以服务管理员身份登录到 Azure Stack 管理门户。

2. 浏览到**应用服务** -> **机密**。 

3. 选择**下载机密**。

   ![下载机密](./media/app-service-back-up/download-secrets.png)

4. 准备好下载机密时，单击**保存**，并将存储的应用服务机密 (**SystemSecrets.JSON**) 在安全位置的文件。 

   ![保存机密](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> 每次应用服务机密轮换时重复这些步骤。

## <a name="back-up-the-app-service-databases"></a>应用服务的数据库备份
若要还原应用服务，将需要**Appservice_hosting**并**Appservice_metering**数据库备份。 我们建议使用 SQL Server 维护计划或 Azure 备份服务器来确保这些数据库中备份和定期安全地保存。 但是，创建确保常规 SQL 备份的任何方法可以使用。

若要手动备份这些数据库登录到 SQL Server 时，可以使用以下 PowerShell 命令：

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> 如果你需要备份 SQL AlwaysOn 数据库，请按照[这些说明](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017)。 

所有数据库已成功都备份后，请将.bak 文件复制到安全的位置以及应用服务的机密信息。

## <a name="back-up-the-app-service-file-share"></a>备份应用服务文件共享
应用服务租户应用程序信息存储在中的文件共享。 这必须备份定期与应用服务数据库一起，以便如果要求还原时，尽可能少的数据将丢失。 

若要备份的内容，可以使用 Azure 备份服务器或另一种方法要定期复制文件共享的应用服务文件共享内容的位置保存所有以前的恢复信息。 

例如，可以使用以下步骤以使用 Windows PowerShell (而不是 PowerShell ISE) 控制台会话中的 robocopy:

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>后续步骤
[还原 Azure Stack 上的应用服务](app-service-recover.md)