---
title: Azure Stack 上的应用服务 update 4 发行说明 |Microsoft Docs
description: 了解有关新增功能更新中四个 Azure Stack 上的应用服务、 已知的问题和下载更新的位置。
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
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 80948b973e6d20b4760e97311c5a65886cf91f8f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617007"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>应用服务在 Azure Stack update 4 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了改进和修补程序在 Azure 应用服务中的 Azure Stack Update 4 和任何已知的问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 适用于 Azure Stack 集成系统的 1809年更新或部署 Azure 应用服务 1.4 之前部署最新的 Azure Stack 开发工具包。
>
>

## <a name="build-reference"></a>内部版本参考

Azure Stack Update 4 生成号上的应用服务是**78.0.13698.5**

### <a name="prerequisites"></a>必备组件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack 上的 Azure 应用服务升级到 1.4 之前：

- 确保所有角色都都已准备 Azure Stack 管理员门户中的 Azure 应用服务管理

- 备份应用服务和 Master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 备份租户应用程序内容的文件共享

- 联合自定义脚本扩展从 Marketplace 1.9 版

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Update 4 上的 azure 应用服务包括以下改进和修补程序：

- 解决方法[CVE 2018 8600](https://aka.ms/CVE20188600)跨站点脚本漏洞。

- 添加了的对应用服务 2018年-02-01 API 版本

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - 添加了的 NodeJS 10.6.0
  - 添加了的 NPM 6.1.0
  - 添加了 Zulu OpenJDK 8.31.0.2
  - 添加了的 Tomcat 8.5.34 和 9.0.12
  - 添加了 PHP 版本：
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - 更新到 Python 版本：
    - 2.7.15
    - 3.6.6
  - 已将适用于 Windows 的 Git 更新到 v 2.17.1.2
  - 已更新到 78.11022.3613 Kudu
  
- **对所有角色的基础操作系统的更新**：
  - [2018 10 Windows Server 2016 的基于 x64 的系统 (KB4462928) 的累积更新](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- 部署 Wordpress; 时解析模板验证问题DNN;和 Orchard CMS 库项

- Azure Stack 旋转的 Azure 资源管理器客户端证书时解决配置问题

- 还原应用服务租户门户中的跨域资源共享设置中的功能

- 资源提供程序控制平面无法连接到已配置的 SQL Server 实例时在应用服务管理门户体验中显示错误消息

- 确保在新的函数应用程序中指定的自定义存储连接字符串中指定终结点

### <a name="post-update-steps-optional"></a>（可选） 后更新步骤

对于希望在 Azure Stack 部署的现有 Azure App service 迁移为包含的数据库的客户，Azure 应用服务在 Azure Stack 1.4 更新完成后执行这些步骤：

> [!IMPORTANT]
> 迁移过程将大约 5 到 10 分钟。  该过程包括终止现有的数据库登录会话。  规划的停机时间和验证 Azure 应用服务上迁移 Azure Stack 后的迁移。  如果您更新到 Azure Stack 1.3 上的 Azure 应用服务，则不需要执行这些步骤后完成这些步骤。
>
>

1. [将 AppService 数据库（appservice_hosting 和 appservice_metering）添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. 启用包含的数据库
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. 转换为部分包含数据库，则转换会产生停机时间根据需要终止所有活动会话

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

1. 检查 SQL Server 是否启用了包含

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. 检查现有的包含的行为
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 辅助角色是无法访问文件服务器时在现有的虚拟网络中部署应用服务和文件服务器上才专用网络，Azure Stack 部署文档上的 Azure 应用服务中所述。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中 WorkersNsg 并添加出站安全规则具有以下属性：
 * 源：任何
 * 源端口范围：*
 * 目标：IP 地址
 * 目标 IP 地址范围：文件服务器的 IP 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：允许
 * 优先级：700
 * 名称：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

中的文档，请参阅[Azure Stack 1809 发行说明](azure-stack-update-1809.md)

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
