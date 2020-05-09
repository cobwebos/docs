---
title: 使用 Azure SQL 数据库托管实例代理计划 SSIS 包的执行
description: 了解如何使用 Azure SQL 数据库托管实例代理来安排 SSIS 包执行。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: f230e4d33686b006b20e856d5e8033847e3f3d67
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628480"
---
# <a name="schedule-ssis-package-executions-by-using-azure-sql-database-managed-instance-agent"></a>使用 Azure SQL 数据库托管实例代理计划 SSIS 包的执行

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何使用 Azure SQL 数据库托管实例代理运行 SQL Server Integration Services （SSIS）包。 此功能提供的行为类似于在本地环境中使用 SQL Server 代理安排 SSIS 包的时间。

利用此功能，你可以运行存储在 Azure SQL 数据库托管实例或文件系统（如 Azure 文件）中的 SSISDB 中的 SSIS 包。

## <a name="prerequisites"></a>先决条件
若要使用此功能，请[下载](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)并安装最新版本的 SQL SERVER MANAGEMENT STUDIO （SSMS），即18.5 版。

还需要在 Azure 数据工厂中[预配 AZURE SSIS 集成运行时](tutorial-create-azure-ssis-runtime-portal.md)。 它使用 Azure SQL 数据库托管实例作为终结点服务器。 

## <a name="run-an-ssis-package-in-ssisdb"></a>在 SSISDB 中运行 SSIS 包
在此过程中，将使用 Azure SQL 数据库托管实例代理来调用在 SSISDB 中存储的 SSIS 包。

1. 在最新版本的 SSMS 中，连接到 Azure SQL 数据库托管实例。
1. 创建新的代理作业和新的作业步骤。 在 " **SQL Server 代理**" 下，右键单击 "**作业**" 文件夹，然后选择 "**新建作业**"。

   ![用于创建新代理作业的选项](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 "**新建作业步骤**" 页上，选择 " **SQL Server Integration Services 包**" 作为 "类型"。

   ![用于创建新 SSIS 作业步骤的选项](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 "**包**" 选项卡上，选择 " **SSIS 目录**" 作为 "包源类型"。
1. 由于 SSISDB 位于 Azure SQL 数据库托管实例中，因此不需要指定身份验证。
1. 指定 SSISDB 中的 SSIS 包。

   ![包选项卡，其中包含包源类型选项](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. 在 "**配置**" 选项卡上，您可以：
  
   - 在 "**参数**" 下指定参数值。
   - 在 "**连接管理器**" 下覆盖值。
   - 重写属性，然后在 "**高级**" 下选择日志记录级别。

   ![带有包源类型选择的 "配置" 选项卡](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. 选择 **"确定"** 以保存代理作业配置。
1. 启动代理作业来运行 SSIS 包。


## <a name="run-an-ssis-package-in-the-file-system"></a>在文件系统中运行 SSIS 包
在此过程中，将使用 Azure SQL 数据库托管实例代理来运行存储在文件系统中的 SSIS 包。

1. 在最新版本的 SSMS 中，连接到 Azure SQL 数据库托管实例。
1. 创建新的代理作业和新的作业步骤。 在 " **SQL Server 代理**" 下，右键单击 "**作业**" 文件夹，然后选择 "**新建作业**"。

   ![用于创建新代理作业的选项](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 "**新建作业步骤**" 页上，选择 " **SQL Server Integration Services 包**" 作为 "类型"。

   ![用于创建新 SSIS 作业步骤的选项](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 "**包**" 选项卡上：

   1. 对于 "**包源**"，请选择 "**文件系统**"。
   
   1. 对于**文件源类型**：   

      - 如果将包上传到 Azure 文件，请选择 " **azure 文件共享**"。

        ![文件源类型的选项](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        包路径为**`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`**。
      
        在 "**包文件访问凭据**" 下，输入用于访问 azure 文件的 azure 文件帐户名和帐户密钥。 域设置为**Azure**。

      - 如果将包上传到网络共享，请选择 "**网络共享**"。
      
        包路径是包文件的 UNC 路径，扩展名为 .dtsx。
      
        输入相应的域、用户名和密码以访问网络共享包文件。
   1. 如果使用密码对包文件进行加密，请选择 "**加密密码**"，然后输入密码。
1. 如果需要配置文件来运行 SSIS 包，请在 "**配置**" 选项卡上输入配置文件路径。
   如果将配置存储在 Azure 文件中，则其配置路径将**`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** 为。
1. 在 "**执行选项**" 选项卡上，可以选择是使用**Windows 身份验证**还是**32 位运行时**来运行 SSIS 包。
1. 在 "**日志记录**" 选项卡上，可以选择日志记录路径和相应的日志记录访问凭据以存储日志文件。 
   默认情况下，日志记录路径与包文件夹路径相同，日志记录访问凭据与包访问凭据相同。
   如果将日志存储在 Azure 文件中，则日志记录路径将**`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** 为。
1. 在 "**设置值**" 选项卡上，可以输入属性路径和值以替代包属性。
 
   例如，若要覆盖用户变量的值，请按以下格式输入其路径： **`\Package.Variables[User::<variable name>].Value`**。
1. 选择 **"确定"** 以保存代理作业配置。
1. 启动代理作业来运行 SSIS 包。


## <a name="cancel-ssis-package-execution"></a>取消 SSIS 包执行
若要从 Azure SQL 数据库托管实例代理作业取消包执行，请执行以下步骤，而不是直接停止代理作业：

1. 查找**sysjobs**中的 SQL 代理**jobId** 。
1. 使用以下查询，基于作业 ID 查找相应的 SSIS **executionId** ：
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. 右键单击 SSISDB 目录，然后选择 "**活动操作**"。

   ![SSISDB 目录的快捷菜单上的 "活动操作"](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. 基于**executionId**停止相应的操作。

## <a name="next-steps"></a>后续步骤
你还可以使用 Azure 数据工厂来计划 SSIS 包。 有关分步说明，请参阅[Azure 数据工厂事件触发器](how-to-create-event-trigger.md)。 
