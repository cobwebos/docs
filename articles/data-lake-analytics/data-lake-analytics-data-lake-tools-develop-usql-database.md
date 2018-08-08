---
title: 使用 U-SQL 数据库项目开发 Azure Data Lake 的 U-SQL 数据库 | Microsoft Docs
description: 了解如何使用针对 Visual Studio 的 Azure Data Lake 工具开发 U-SQL 数据库。
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
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 1fee28ad185629b0cb26abb54cc2e196fca791e5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364043"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>使用 U-SQL 数据库项目开发 Azure Data Lake 的 U-SQL 数据库

U-SQL 数据库为表格中的非结构化数据和托管的结构化数据提供了结构化的视图。 它还提供用于整理结构化数据和自定义代码的常规元数据目录系统。 数据库是将这些相关对象组在一起的概念。

了解有关 [U-SQL 数据库和数据定义语言 (DDL)](https://msdn.microsoft.com/azure/data-lake-analytics/u-sql/data-definition-language-ddl-statements-u-sql)的详细信息。 

U-SQL 数据库项目是 Visual Studio 中的一种项目类型，可帮助开发人员快速轻松地开发、管理和部署其 U-SQL 数据库。

## <a name="create-a-u-sql-database-project"></a>创建 U-SQL 数据库项目

针对 Visual Studio 的 Azure Data Lake 工具在版本 2.3.3000.0 之后添加了一个名为 U-SQL 数据库项目的新项目模板。 若要创建 U-SQL 项目，请选择“文件”>“新建”>“项目”。 可在“Azure Data Lake”>“U-SQL”节点下找到“U-SQL 数据库项目”。

![针对 Visual Studio 的 Data Lake 工具 - 创建 U-SQL 数据库项目](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>使用数据库项目开发 U-SQL 数据库项目

右键单击 U-SQL 数据库项目。 选择“添加”>“新建项目”。 可在“添加新项目”向导中找到所有受支持的新对象类型。 

对于非程序集对象（例如表值函数），在添加新项目后会创建新的 U-SQL 脚本。 你可以在编辑器中开始为该对象开发 DDL 语句。

对于程序集对象，该工具提供了一个用户友好的 UI 编辑器，可帮助注册程序集并部署 DLL 文件和其他文件。 以下步骤展示了如何将程序集对象定义添加到 U-SQL 数据库项目：

1.  为 U-SQL 数据库项目添加对包含 UDO/UDAG/UDF 的 C# 项目的引用。

    ![针对 Visual Studio 的 Data Lake 工具 - 添加 U-SQL 数据库项目引用](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![针对 Visual Studio 的 Data Lake 工具 - 添加 U-SQL 数据库项目引用](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  在程序集设计视图中，从“从引用创建程序集”下拉菜单中选择引用的程序集。

    ![针对 Visual Studio 的 Data Lake 工具 - 从引用创建程序集](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  添加“托管的依赖项”和“其他文件”（若有）。 添加其他文件时，该工具会使用相对路径来确保它稍后可在本地计算机和生成计算机上找到程序集。 

@_DeployTempDirectory 是一个预定义的变量，它将该工具指向生成输出文件夹。 在生成输出文件夹下，每个程序集都有按程序集名称命名的子文件夹。 此子文件夹中包含所有 DLL 和其他文件。 
 
## <a name="build-a-u-sql-database-project"></a>生成 U-SQL 数据库项目

U-SQL 数据库项目的生成输出是一个 U-SQL 数据库部署包，名称的后缀为 `.usqldbpack`。 `.usqldbpack` 包是一个 zip 文件，其中包含“DDL”文件夹中单个 U-SQL 脚本中的所有 DDL 语句，以及“Temp”文件夹中程序集的所有 DLL 和其他文件。

详细了解[如何使用 MSBuild 命令行和 Visual Studio Team Service 生成任务生成 U-SQL 数据库项目](data-lake-analytics-cicd-overview.md)。

## <a name="deploy-a-u-sql-database"></a>部署 U-SQL 数据库

可以使用 Visual Studio 或部署 SDK 将 .usqldbpack 包部署到本地帐户或 Azure Data Lake Analytics 帐户。 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>在 Visual Studio 中部署 U-SQL 数据库

你可以在 Visual Studio 中通过 U-SQL 数据库项目或 .usqldbpack 包部署 U-SQL 数据库。

#### <a name="deploy-through-a-u-sql-database-project"></a>通过 U-SQL 数据库项目进行部署

1.  右键单击 U-SQL 数据库项目，然后选择“部署”。
2.  在“部署 U-SQL 数据库”向导中，选择要将数据库部署到的“ADLA 帐户”。 支持本地帐户和 ADLA 帐户。
3.  “数据库源”会自动填充，并指向项目生成输出文件夹中的 .usqldbpack 包。
4.  在“数据库名称”中键入名称以创建数据库。 如果 Azure Data Lake Analytics 目标帐户中存在名称相同的数据库，则会创建在数据库项目中定义的所有对象，无需重新创建数据库。
5.  要部署 U-SQL 数据库，选择“提交”。 上传所有资源（程序集和其他文件）并提交包含所有 DDL 语句的 U-SQL 作业。

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库项目](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库项目向导](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>通过 U-SQL 数据库部署包进行部署

1.  打开“服务器资源管理器”。 然后展开要将数据库部署到的“Azure Data Lake Analytics 帐户”。
2.  右键单击“U-SQL 数据库”并选择“部署数据库”。
3.  将“数据库源”设置为 U-SQL 数据库部署包（.usqldbpack 文件）路径。
4.  键入数据库名称以创建数据库。 如果目标 Azure Data Lake Analytics 帐户中存在名称相同的数据库，则会创建在数据库项目中定义的所有对象，无需重新创建数据库。

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库包](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![针对 Visual Studio 的 Data Lake 工具 - 部署 U-SQL 数据库包向导](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>使用 SDK 部署 U-SQL 数据库

`PackageDeploymentTool.exe` 提供帮助部署 U-SQL 数据库的编程和命令行接口。 SDK 包含在 [U-SQL SDK Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位于 `build/runtime/PackageDeploymentTool.exe`。

[了解有关 SDK 和如何为 U-SQL 数据库部署设置 CI/CD 管道的详细信息](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-visual-studio-team-service)。

## <a name="reference-a-u-sql-database-project"></a>引用 U-SQL 数据库项目

U-SQL 项目可以引用 U-SQL 数据库项目。 该引用将影响两个工作负载：

- 项目生成：在生成 U-SQL 脚本之前设置引用的数据库环境。 
- 针对（本地项目）帐户的本地运行：在执行 U-SQL 脚本之前，将引用的数据库环境部署到（本地项目）帐户。 [在此处详细了解本地运行以及（本地计算机）和（本地项目）帐户之间的区别](data-lake-analytics-data-lake-tools-local-run.md)。

### <a name="how-to-add-a-u-sql-database-reference"></a>如何添加 U-SQL 数据库引用

1. 右键单击“解决方案资源管理器”中的 U-SQL 项目，然后选择“添加 U-SQL 数据库引用...”。

    ![针对 Visual Studio 的 Data Lake 工具 - 添加数据库项目引用](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. 从当前解决方案或 U-SQL 数据库包文件中的 U-SQL 数据库项目配置数据库引用。
3. 提供数据库的名称。

    ![针对 Visual Studio 的 Data Lake 工具添加数据库项目引用向导](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>后续步骤

- [如何为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)
- [如何测试 Azure Data Lake Analytics 代码](data-lake-analytics-cicd-test.md)
- [在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)
