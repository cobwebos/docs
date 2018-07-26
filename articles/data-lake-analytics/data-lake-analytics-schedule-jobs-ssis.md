---
title: 使用 SSIS 计划 Azure Data Lake Analytics U-SQL 作业 | Microsoft Docs
description: 了解如何使用 SQL Server Integration Services 计划 U-SQL 作业。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/17/2018
ms.author: yanacai
ms.openlocfilehash: 919ec4e26c2da945a0623e772dc00d378a583b69
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126739"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>使用 SQL Server Integration Services (SSIS) 计划 U-SQL 作业

本文介绍如何使用 SQL Server Integration Service (SSIS) 安排和创建 U-SQL 作业。 

## <a name="prerequisites"></a>先决条件

[用于 Integration Services 的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)提供 [Azure Data Lake Analytics 任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)和 [Azure Data Lake Analytics 连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)，该管理器可帮助连接 Azure Data Lake Analytics 服务。 若要使用此任务，请务必：

- [下载并安装适用于 Visual Studio 的 SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [安装适用于 Integration Services (SSIS) 的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics 任务

Azure Data Lake Analytics 任务允许用户向 Azure Data Lake Analytics 帐户提交 U-SQL 作业。 

[了解如何配置 Azure Data Lake Analytics 任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)。

![SSIS 中的 Azure Data Lake Analytics 任务](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

可使用 SSIS 内置函数和任务从不同位置获取 U-SQL 脚本，以下场景介绍如何针对不同用户案例配置 U-SQL 脚本。

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>场景 1-使用内联脚本调用 tvf 和存储过程

在 Azure Data Lake Analytics 任务编辑器中，将 SourceType 配置为 DiretInput，并将 U-SQL 语句置于 USQLStatemnt 中。

为了便于维护和代码管理，只需将简短的 U-SQL 脚本作为内联脚本，例如，可以调用 U-SQL 数据库中现有的表值函数和存储过程。 

![在 SSIS 任务中编辑内联 U-SQL 脚本](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

相关文章：[如何将参数传递到存储过程](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>场景 2-使用 Azure Data Lake Store 中的 U-SQL 文件

通过使用 Azure 功能包中的 Azure Data Lake Store 文件系统任务，还可使用 Azure Data Lake Store 中的 U-SQL 文件。 通过这种方法，可使用存储在云上的脚本。

按照以下步骤设置 Azure Data Lake Store 文件系统任务和 Azure Data Lake Analytics 任务之间的连接。

### <a name="set-task-control-flow"></a>设置任务控制流

在 SSIS 包设计视图中，添加一个 Azure Data Lake Store 文件系统任务、一个 Foreach 循环容器和一个位于 Foreach 循环容器内的 Azure Data Lake Analytics 任务。 Azure Data Lake Store 文件系统任务有助于将 ADLS 帐户中的 U-SQL 文件下载到临时文件夹。 Foreach 循环容器和 Azure Data Lake Analytics 任务有助于将临时文件夹下的每个 U-SQL 文件作为 U-SQL 作业提交给 Azure Data Lake Analytics 帐户。

![使用 Azure Data Lake Store 中的 U-SQL 文件](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>配置 Azure Data Lake Store 文件系统任务

1. 将“操作”设置为“CopyFromADLS”。
2. 设置 AzureDataLakeConnection，详细了解 [Azure Data Lake Store 连接管理器](https://docs.microsoft.com/en-us/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017)。
3. 设置 AzureDataLakeDirectory。 指向存储 U-SQL 脚本的文件夹。 使用相对于 Azure Data Lake Store 帐户根文件夹的相对路径。
4. 将“目标”设置为缓存下载的 U-SQL 脚本的文件夹。 此文件夹路径将在 Foreach 循环容器中用于 U-SQL 作业提交。 

![配置 Azure Data Lake Store 文件系统任务](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[详细了解 Azure Data Lake Store 文件系统任务](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017)。

### <a name="configure-foreach-loop-container"></a>配置 Foreach 循环容器

1. 在“集合”页上，将“枚举器”设置为“Foreach 文件枚举器”。

2. 将“枚举器配置”组下的“文件夹”设置为包含下载的 U-SQL 脚本的临时文件夹。

3. 将“枚举器配置”下的“文件”设置为 `*.usql`，使循环容器仅捕获以 `.usql` 结尾的文件。

    ![配置 Foreach 循环容器](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. 在“变量映射”页中，添加一个用户定义变量，获取每个 U-SQL 文件的文件名。 将“索引”设置为 0 以获取文件名。 在本示例中，定义一个名为 `User::FileName` 的变量。 此变量将用于动态地获取 U-SQL 脚本文件连接，并在 Azure Data Lake Analytics 任务中设置 U-SQL 作业名称。

    ![配置 Foreach 循环容器以获取文件名](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>配置 Azure Data Lake Analytics 任务 

1. 将“SourceType”设置为“FileConnection”。

2. 将“FileConnection”设置为指向从 Foreach 循环容器返回的文件对象的文件连接。
    
    创建此文件连接：

    1. 在 FileConnection 设置中选择 <New Connection...>。
    2. 将“使用类型”设置为“现有文件”，并将“文件”设置为任何现有文件的文件路径。

        ![配置 Foreach 循环容器](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

    3. 在“连接管理器”视图中，右键单击刚才创建的文件连接，选择“属性”。

    4. 在“属性”窗口中，展开“表达式”，并将“ConnectionString”设置为 Foreach 循环容器中定义的变量，例如 `@[User::FileName]`。

        ![配置 Foreach 循环容器](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. 将“AzureDataLakeAnalyticsConnection”设置为要向其提交作业的 Azure Data Lake Analytics 帐户。 详细了解 [Azure Data Lake Analytics 连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)。

4. 设置其他作业配置。 [了解详细信息](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)。

5. 使用“表达式”动态地设置 U-SQL 作业名称：

    1. 在“表达式”页中，为“JobName”添加新的表达式键值对。
    2. 将 JobName 的值设置为在 Foreach 循环容器中定义的变量，例如 `@[User::FileName]`。
    
        ![为 U-SQL 作业名称配置 SSIS 表达式](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>场景 3-使用 Azure Blob 存储中的 U-SQL 文件

通过使用 Azure 功能包中的 Azure Blob 下载任务，可使用 Azure Blob 存储中的 U-SQL 文件。 通过这种方法，可使用云中的脚本。

这些步骤与[情景 2：使用 Azure Data Lake Store 中的 U-SQL 文件](#scenario-2-use-u-sql-files-in-azure-data-lake-store)中的步骤类似。 将 Azure Data Lake Store 文件系统任务更改为 Azure Blob 下载任务。 [详细了解 Azure Blob 下载任务](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017)。

控制流如下所示。

![使用 Azure Data Lake Store 中的 U-SQL 文件](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>场景 4-使用本地计算机上的 U-SQL 文件

除了使用存储在云中的 U-SQL 文件，还可以使用本地计算机上的文件或与 SSIS 包一起部署的文件。

1. 右键单击 SSIS 项目中的“连接管理器”，然后选择“新建连接管理器”。

2. 选择“文件”类型，然后单击“添加...”。

3. 将“使用类型”设置为“现有文件”，并将“文件”设置为本地计算机上的文件。

    ![添加本地文件的文件连接](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. 添加“Azure Data Lake Analytics 任务”并：
    1. 将“SourceType”设置为“FileConnection”。
    2. 将“FileConnection”设置为刚创建的文件连接。

5. 完成 Azure Data Lake Analytics 任务的其他配置。

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>场景 5-使用 SSIS 变量中的 U-SQL 语句

在某些情况下，可能需要动态生成 U-SQL 语句。 可将“SSIS 变量”与“SSIS 表达式”以及其他 SSIS 任务（如脚本任务）一起使用，帮助动态地生成 U-SQL 语句。

1. 通过“SSIS”>“变量”顶层菜单打开“变量”工具窗口。

2. 添加 SSIS 变量并直接设置值，或者使用“表达式”生成值。

3. 添加“Azure Data Lake Analytics 任务”并：
    1. 将“SourceType”设置为“变量”。
    2. 将“SourceVariable”设置为刚创建的 SSIS 变量。

4. 完成 Azure Data Lake Analytics 任务的其他配置。

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>场景 6-将参数传递到 U-SQL 脚本

在某些情况下，可能需要在 U-SQL 脚本中动态地设置 U-SQL 变量值。 Azure Data Lake Analytics 任务中的“参数映射”功能可在这种情况下提供帮助。 通常有两种典型的用户案例：

- 根据当前日期和时间动态设置输入和输出文件路径变量。
- 设置存储过程的参数。

[详细了解如何设置 U-SQL 脚本的参数](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration)。

## <a name="next-steps"></a>后续步骤

- [在 Azure 中运行 SSIS 包](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [适用于 Integration Services (SSIS) 的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [使用 Azure 数据工厂计划 U-SQL 作业](https://docs.microsoft.com/en-us/azure/data-factory/transform-data-using-data-lake-analytics)

