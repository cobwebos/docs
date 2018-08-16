---
title: Azure Stack 上的应用服务 update 3 发行说明 |Microsoft Docs
description: 了解有关新增功能更新中三个用于 Azure Stack 上的应用服务、 已知的问题和下载更新的位置。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 7cf41a10f13e2edeb4ab1944c0d38cc7064c02bb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161469"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>应用服务在 Azure Stack update 3 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了改进和修补程序在 Azure 应用服务中的 Azure Stack Update 3 和任何已知的问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 适用于 Azure Stack 集成系统的 1807年更新或部署 Azure 应用服务 1.3 之前部署最新的 Azure Stack 开发工具包。
>
>

## <a name="build-reference"></a>内部版本参考

Azure Stack 更新 3 生成号上的应用服务是**74.0.13698.31**

### <a name="prerequisites"></a>必备组件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

在开始将 Azure Stack 上的 Azure 应用服务升级到 1.3 之前，请确保所有角色都都已准备 Azure Stack 管理员门户中的 Azure 应用服务管理

![应用服务角色状态](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack 更新 3 上的 azure 应用服务包括以下改进和修补程序：

- 针对 Azure 应用服务资源提供程序数据库的 SQL Server Always On 的使用的支持。

- 新环境参数添加到创建 AADIdentityApp 帮助程序脚本，以帮助目标不同的 AAD 区域。

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - 添加了的 ASP.Net Core 2.1.2
  - 添加了的 NodeJS 10.0.0
  - 添加了 Zulu OpenJDK 8.30.0.1
  - 添加了的 Tomcat 8.5.31 和 9.0.8
  - 添加了的 PHP 版本：
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - 添加了的 Wincache 2.0.0.8
  - 更新的 Git 的 Windows 到 v 2.17.1.2
  - 已更新到 74.10611.3437 Kudu

### <a name="post-update-steps-optional"></a>发布更新步骤 （可选）

对于希望在 Azure Stack 部署的现有 Azure App service 迁移为包含的数据库的客户，Azure 应用服务在 Azure Stack 1.3 更新完成后执行这些步骤：

> [!IMPORTANT]
> 此过程大约需要 5 到 10 分钟。  此过程涉及到终止现有的数据库登录会话。  规划的停机时间和验证 Azure 应用服务上迁移 Azure Stack 后的迁移
>
>

1. 添加[数据库到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. 启用包含数据库
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 将转换为部分包含的数据库。  此步骤将产生停机时间，因为需要终止所有活动会话

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

验证

1. 检查 SQL Server 是否启用包含

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 检查现有的包含的行为
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

中的文档，请参阅[Azure Stack 1807 发行说明](azure-stack-update-1807.md)

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
