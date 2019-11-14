---
title: 将 R 包添加到机器学习服务（预览版）
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文介绍如何安装尚未安装在 Azure SQL 数据库机器学习服务（预览版）中的 R 包。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: ce85f45d823df42e70af53824e175968439621d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819872"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>将 R 包添加到 Azure SQL 数据库机器学习服务（预览版）

本文介绍如何将 R 包添加到 Azure SQL 数据库机器学习服务（预览版）。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

- 在本地计算机上安装 [R](https://www.r-project.org) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。 R 适用于 Windows、MacOS 和 Linux。 本文假设使用 Windows。

- 本文包含一个演示如何使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) 在 Azure SQL 数据库中运行 R 脚本的示例。 可以使用其他数据库管理或查询工具运行 R 脚本，但本示例假设使用 Azure Data Studio 或 SSMS。
   
> [!NOTE]
> 使用 Azure Data Studio 或 SSMS 中的 **sp_execute_external_script** 运行 R 脚本无法安装包。 只能使用本文中所述的 R 命令行与 RStudio 来安装和删除包。 安装包后，可以使用 **sp_execute_external_script** 访问 R 脚本中的包函数。

## <a name="list-r-packages"></a>列出 R 包

Microsoft 在 Azure SQL 数据库中提供许多预装有机器学习服务的 R 包。
在 Azure Data Studio 或 SSMS 中运行以下命令可以查看已安装的 R 包列表。

1. 打开 Azure Data Studio 或 SSMS 并连接到 Azure SQL 数据库。

1. 运行以下命令：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

输出应如下所示。

**结果**

![R 中的已安装包](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>使用 sqlmlutils 添加包

如需使用尚未安装在 Azure SQL 数据库中的包，可以使用 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 来安装该包。 **sqlmlutils** 包旨在帮助用户与 SQL 数据库（SQL Server 和 Azure SQL 数据库）交互，以及从 R 或 Python 客户端执行 SQL 中的 R 或 Python 代码。 目前，Azure SQL 数据库仅支持 R 版本的 **sqlmlutils**。

以下示例将安装 **[glue](https://cran.r-project.org/web/packages/glue/)** 包，该包可以内插字符串及设置其格式。 这些步骤将安装 **sqlmlutils** 和 **RODBCext**（**sqlmlutils** 的必备组件），并添加 **glue** 包。

### <a name="install-sqlmlutils"></a>安装 **sqlmlutils**

1. 将最新的 **sqlmlutils** zip 文件从 https://github.com/Microsoft/sqlmlutils/tree/master/R/dist 下载到本地计算机。 无需解压缩该文件。

1. 打开**命令提示符**并运行以下命令，以在本地计算机上安装 **RODBCext** 和 **sqlmlutils**。 替换下载的 **sqlmlutils** zip 文件的完整路径（本示例假设该文件位于 Documents 文件夹中）。
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    应会看到如下所示的输出。

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > 如果收到错误“'R' 未识别为内部或外部命令、可运行程序或批处理文件”，则可能意味着 R.exe 的路径未包括在 Windows 的 **PATH** 环境变量中。 可将路径添加到该环境变量，或者在命令提示符中导航到该文件夹（例如 `cd C:\Program Files\R\R-3.5.3\bin`），然后重试该命令。

### <a name="add-the-package"></a>添加包

1. 打开 RStudio 并创建新的 **R 脚本**文件。 

1. 使用以下 R 代码通过 **sqlmlutils** 安装 **glue** 包。 请将相应部分替换为自己的 Azure SQL 数据库连接信息。

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 公共范围适用于数据库管理员安装供所有用户使用的包。 如果选择专用范围，则包只能供安装该包的用户使用。 如果不指定范围，则默认范围为 **PRIVATE**。

### <a name="verify-the-package"></a>验证包

在 RStudio 中运行以下 R 脚本，确认是否已安装 **glue** 包。 使用在上一步骤中定义的相同**连接**。

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**结果**

![RTestData 表的内容](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>使用包

安装包后，可在 R 脚本中通过 **sp_execute_external_script** 来使用它。

1. 打开 Azure Data Studio 或 SSMS 并连接到 Azure SQL 数据库。

1. 运行以下命令：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    将在“消息”选项卡中看到以下结果。 

    **结果**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>删除包

若要删除包，请在 RStudio 中运行以下 R 脚本。 使用前面定义的相同**连接**。

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> 将 R 包安装到 Azure SQL 数据库的另一种方法是使用 **CREATE EXTERNAL LIBRARY** T-SQL 语句从字节流上传 R 包。 请参阅 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 参考文档中的[从字节流创建库](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream)。

## <a name="next-steps"></a>后续步骤

有关使用 R 的 Azure SQL 数据库机器学习服务（预览版）的详细信息，请参阅以下文章。

- [使用 R 的 Azure SQL 数据库机器学习服务（预览版）](sql-database-machine-learning-services-overview.md)
- [使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数](sql-database-machine-learning-services-functions.md)
- [在 Azure SQL 数据库机器学习服务中使用 R 和 SQL 数据（预览版）](sql-database-machine-learning-services-data-issues.md)