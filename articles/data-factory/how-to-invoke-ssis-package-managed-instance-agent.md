---
title: 按 Azure SQL 托管实例代理执行 SSIS 包
description: 了解如何按 Azure SQL 托管实例代理执行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394717"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>按 Azure SQL 托管实例代理执行 SSIS 包
本文介绍如何使用 Azure SQL 托管实例代理运行 SQL 服务器集成服务 （SSIS） 包。 此功能提供类似行为，就像在预环境中按 SQL Server 代理计划 SSIS 包一样。

使用此功能，可以运行存储在 Azure SQL 托管实例或文件系统（如 Azure 文件）SSISDB 中的 SSIS 包。

## <a name="prerequisites"></a>先决条件
要使用此功能，请下载并安装最新版本的 SSMS，即版本 18.5 或更高版本。 从[此网站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下载它。

还需要在 Azure 数据工厂中预配 Azure-SSIS 集成运行时，该运行时使用 Azure SQL 托管实例作为终结点服务器。 如果尚未预配它，请按照[教程](tutorial-create-azure-ssis-runtime-portal.md)中的说明进行预配。 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>按 Azure SQL 托管实例代理在 SSISDB 中运行 SSIS 包
在此步骤中，使用 Azure SQL 托管实例代理调用存储在 Azure SQL 托管实例中的 SSISDB 中的 SSIS 包。
1. 在最新版本的 SSMS 中，连接到 Azure SQL 托管实例。
2. 创建新的代理作业和新的作业步骤。

![新代理作业](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 在 **"新建作业步骤"** 页中，选择**SQL 服务器集成服务包**类型。

![新的 SSIS 作业步骤](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. 在 **"包"** 选项卡中，选择**SSIS 目录**作为包源类型。
5. 由于 SSISDB 位于同一 Azure SQL 托管实例中，因此无需指定身份验证。
6. 从 SSISDB 指定 SSIS 包。

![包源类型 - SSIS 目录](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. 在 **"配置"** 选项卡中，可以指定**参数**值、覆盖**连接管理器**中的值、覆盖**属性**并选择**日志记录级别**。

![包源类型 - SSIS 目录配置](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. 完成上述所有配置后，单击 **"确定"** 以保存代理作业配置。
9. 启动代理作业以执行 SSIS 包。


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>由 Azure SQL 托管实例代理在文件系统中运行 SSIS 包
在此步骤中，您可以使用 Azure SQL 托管实例代理调用存储在文件系统中运行的 SSIS 包。
1. 在最新版本的 SSMS 中，连接到 Azure SQL 托管实例。
2. 创建新的代理作业和新的作业步骤。

   ![新代理作业](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 在 **"新建作业步骤"** 页中，选择**SQL 服务器集成服务包**类型。

   ![新的 SSIS 作业步骤](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. 在 **"包"** 选项卡中，选择**文件系统**作为包源类型。

   ![包源类型 - 文件系统](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. 如果包上载到 Azure 文件，请选择**Azure 文件共享**作为文件源类型。
      - 包路径是**\\<storage account name>.file.core.windows.net\<文件共享名\<>包名称>.dtsx**
      - 在**包文件访问凭据**中键入 Azure 文件帐户名和帐户密钥以访问 Azure 文件。 域设置为**Azure**。
   2. 如果包上载到网络共享，请选择 **"网络共享**"作为文件源类型。
      - 包路径是包文件的**UNC 路径**及其 dtsx 扩展名。
      - 键入相应的**域**、**用户名**和**密码**以访问网络共享包文件。
   3. 如果您的包文件使用密码加密，请选择 **"加密密码**"并键入密码。

 5. 在 **"配置"** 选项卡中，如果需要配置文件来执行 SSIS 包，请键入**配置文件路径**。
 6. 在 **"执行"选项**选项卡中，您可以选择是使用**窗口身份验证**还是**使用 32 位运行时**来执行 SSIS 包。
 7. 在 **"日志记录"** 选项卡中，您可以选择**日志记录路径**和相应的日志记录访问凭据来存储日志文件。 默认情况下，日志记录路径将与包文件夹路径相同，日志记录访问凭据将与包访问凭据相同。
 8. 在 **"设置值"** 选项卡中，可以在**属性路径**和**值**中键入以覆盖包属性。
 例如，要覆盖用户变量的值，请输入其路径，其格式为以下： **\Package.变量[用户：：<variable name>]值**。
 9. 完成上述所有配置后，单击 **"确定"** 以保存代理作业配置。
 10. 启动代理作业以执行 SSIS 包。


 ## <a name="cancel-ssis-package-execution"></a>取消 SSIS 包执行
 要取消 Azure SQL 托管代理作业中的包执行，应遵循以下步骤，而不是直接停止代理作业。
 1. 从**msdb.dbo.sysjobs**查找 SQL 代理**作业 ID。**
 2. 根据作业 ID 查找相应的 SSIS**执行 ID，** 通过以下查询：
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. 在 SSIS 目录下选择 **"活动操作**"。

    ![包源类型 - 文件系统](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. 停止基于**执行 ID 的**相应操作。

## <a name="next-steps"></a>后续步骤
 您还可以使用 Azure 数据工厂计划 SSIS 包。 有关分步说明，请参阅[Azure 数据工厂事件触发器](how-to-create-event-trigger.md)。 