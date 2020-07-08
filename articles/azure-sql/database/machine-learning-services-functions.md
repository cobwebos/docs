---
title: 编写高级 R 函数
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何使用机器学习服务（预览版）在 Azure SQL 数据库中为高级统计计算编写 R 函数。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84035638"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍如何在 SQL 存储过程中嵌入 R 数学和实用函数。 在 T-SQL 中难以实现的高级统计函数在 R 中只需一行代码就可以实现。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

- 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/)。

- 若要在这些练习中运行示例代码，必须先启用启用了[机器学习服务（使用 R）的 AZURE SQL 数据库](machine-learning-services-overview.md)。

- 确保已安装了最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 你可以使用其他数据库管理或查询工具运行 R 脚本，但在本快速入门中，你将使用 SSMS。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>创建一个存储过程来生成随机数字

为简单起见，让我们使用在 `stats` AZURE SQL 数据库中默认安装并加载的 R 包，机器学习服务（预览版）。 该包包含数百个用于常见统计任务的函数，其中包含 `rnorm` 函数。 给定标准偏差和平均值，此函数使用正态分布生成指定数量的随机数。

例如，以下 R 代码在标准偏差为 3 的情况下返回平均值为 50 的 100 个数字。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

若要从 T-sql 调用此 R 行，请 `sp_execute_external_script` 在 r 脚本参数中运行并添加 r 函数，如下所示：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

如果你希望更轻松地生成不同的一组随机数字，那该怎么办？

与 SQL 结合时，这很简单。 定义一个存储过程，从用户那里获取参数，然后将这些参数作为变量传递给 R 脚本。

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 第一行定义了在执行该存储过程时必需的每个 SQL 输入参数。

- 以 `@params` 开头的行定义了 R 代码使用的所有变量，以及对应的 SQL 数据类型。

- 紧跟在后面的行将 SQL 参数名称映射到对应的 R 变量名称。

现在，你已将 R 函数包装在了一个存储过程中，你可以轻松调用该函数并传入不同的值，如下所示：

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>使用 R 实用工具函数进行故障排除

`utils`默认情况下，安装包提供了用于调查当前 R 环境的多种实用函数。 如果您在 SQL 和外部环境中执行 R 代码的方式不一致，则这些函数会很有用。 例如，你可以使用 R `memory.limit()` 函数获取当前 R 环境的内存。

因为 `utils` 包默认情况下已安装但未加载，因此你必须首先使用 `library()` 函数加载该包。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 许多用户喜欢使用 R 中的系统计时函数（如 `system.time` 和 `proc.time` ）来捕获 r 进程使用的时间，并分析性能问题。
