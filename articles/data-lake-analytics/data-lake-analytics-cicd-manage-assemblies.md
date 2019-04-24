---
title: 在 Azure Data Lake 的 CI/CD 管道中管理 U-SQL 程序集的最佳做法
description: 了解使用 Azure DevOps 在 CI/CD 管道中管理 U-SQL C# 程序集的最佳做法。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 27a873fac8bf2b53ee06780b8a348eaaa5c94e97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334141"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>在 CI/CD 管道中管理 U-SQL 程序集的最佳做法

本文介绍如何使用新引入的 U-SQL 数据库项目管理 U-SQL 程序集源代码。 还介绍如何使用 Azure DevOps 为程序集注册设置持续集成和部署 (CI/CD) 管道。

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>使用 U-SQL 数据库项目来管理程序集源代码

[U-SQL 数据库项目](data-lake-analytics-data-lake-tools-develop-usql-database.md)是 Visual Studio 中的一种项目类型，可帮助开发人员快速轻松地开发、管理和部署其 U-SQL 数据库。 可以使用 U-SQL 数据库项目来管理所有 U-SQL 数据库对象（凭据除外）。 

要管理 C# 程序集源代码和程序集注册 DDL U-SQL 脚本，请使用：

* U-SQL 数据库项目管理程序集注册 U-SQL 脚本。
* 类库（对于 U-SQL 应用程序）来管理用户定义运算符、函数和聚合器 (UDO、UDF 和 UDAG) 的 C# 源代码和依赖项。
* U-SQL 数据库项目引用类库项目。 

U-SQL 数据库项目可引用类库（对于 U-SQL 应用程序）项目。 可以使用从此类库（对于 U-SQL 应用程序）项目中引用的 C# 源代码创建 U-SQL 数据库中注册的程序集。

请按照下列步骤创建项目并添加引用。
1. 通过选择“文件” > “新建” > “项目”创建类库（对于 U-SQL 应用程序）项目。 该项目位于“Azure Data Lake > U-SQL”节点下。

   ![用于 Visual Studio 的 Data Lake 工具 - 创建 C# 类库项目](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. 在类库（对于 U-SQL 应用程序）项目中添加用户定义的 C# 代码。

1. 通过选择“文件” > “新建” > “项目”来创建 U-SQL 项目。 该项目位于“Azure Data Lake > U-SQL”节点下。

   ![用于 Visual Studio 的 Data Lake 工具 - 创建 U-SQL 数据库项目](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. 为 U-SQL 数据库项目添加对 C# 类库项目的引用。

    ![针对 Visual Studio 的 Data Lake 工具 - 添加 U-SQL 数据库项目引用](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![针对 Visual Studio 的 Data Lake 工具 - 添加 U-SQL 数据库项目引用](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. 通过右键单击项目并选择“添加新项目”在 U-SQL 数据库项目中创建程序集脚本。

   ![用于 Visual Studio 的 Data Lake 工具 - 添加程序集脚本](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. 在程序集设计视图中打开程序集脚本。 在“从引用创建程序集”下拉菜单中选择引用的程序集。

    ![针对 Visual Studio 的 Data Lake 工具 - 从引用创建程序集](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. 添加“托管的依赖项”和“其他文件”（若有）。 添加其他文件时，该工具会使用相对路径来确保它稍后可在本地计算机和生成计算机上找到程序集。

**\@_DeployTempDirectory**在编辑器中在底部窗口是一个预定义的变量，该工具指向生成输出文件夹。 在生成输出文件夹下，每个程序集都有按程序集名称命名的子文件夹。 此子文件夹中包含所有 DLL 和其他文件。

## <a name="build-a-u-sql-database-project"></a>生成 U-SQL 数据库项目

U-SQL 数据库项目的生成输出是一个 U-SQL 数据库部署包。 其名称的后缀为 `.usqldbpack`。 `.usqldbpack` 包是一个 zip 文件，其中包含 DDL 文件夹中单个 U-SQL 脚本中的所有 DDL 语句。 所有生成的程序集 .dll 文件和其他文件在 Temp 文件夹中。

## <a name="deploy-a-u-sql-database"></a>部署 U-SQL 数据库

`.usqldbpack` 包可以部署到本地帐户或 Azure Data Lake Analytics 帐户。 使用 Visual Studio 或部署用 SDK。 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>在 Visual Studio 中部署 U-SQL 数据库

可以在 Visual Studio 中通过使用 U-SQL 数据库项目或 `.usqldbpack` 包部署 U-SQL 数据库。

#### <a name="deploy-by-using-a-u-sql-database-project"></a>使用 U-SQL 数据库项目进行部署

1.  右键单击 U-SQL 数据库项目，然后选择“部署”。
2.  在“部署 U-SQL 数据库”向导中，选择要将数据库部署到的“ADLA 帐户”。 支持本地帐户和 ADLA 帐户。
3.  将自动填充“数据库源”。 它指向项目生成输出文件夹中的 .usqldbpack 包。
4.  在“数据库名称”中键入名称以创建数据库。 如果 Azure Data Lake Analytics 目标帐户中存在名称相同的数据库，则会创建在数据库项目中定义的所有对象，无需重新创建数据库。
5.  要部署 U-SQL 数据库，选择“提交”。 系统随即上传所有资源（如程序集和其他文件）。 并提交了一个包含所有 DDL 语句的 U-SQL 作业。

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库项目](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库项目向导](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>在 Azure DevOps 中部署 U-SQL 数据库

`PackageDeploymentTool.exe` 提供帮助部署 U-SQL 数据库的编程和命令行接口。 SDK 包含在 [U-SQL SDK Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位于 `build/runtime/PackageDeploymentTool.exe`。

在 Azure DevOps 中，可以使用命令行任务和此 SDK 为 U-SQL 数据库刷新设置自动化管道。 [了解有关 SDK 和如何为 U-SQL 数据库部署设置 CI/CD 管道的详细信息](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)。

## <a name="next-steps"></a>后续步骤

* [为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)
* [测试 Azure Data Lake Analytics 代码](data-lake-analytics-cicd-test.md)
* [在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)
