---
title: 编写高级的 R 函数
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何在 Azure SQL 数据库使用机器学习服务 （预览版） 中编写高级统计计算的 R 函数。
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013523"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>在 Azure SQL 数据库使用机器学习服务 （预览版） 中编写高级的 R 函数

本文介绍如何将嵌入 R 数学和实用工具函数在 SQL 存储过程。 复杂而无法在 T-SQL 中实现的高级统计函数可以在 R 中使用只有一行代码。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必备组件

- 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/)。

- 若要运行示例代码在这些练习中，首先必须启用机器学习服务 （使用 R) Azure SQL 数据库。 在发布公共预览版期间，Microsoft 会将你加入该版本并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

- 请确保已安装最新[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 可以运行 R 脚本使用其他数据库管理或查询工具，但在本快速入门中，您将使用 SSMS。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>创建存储的过程来生成随机数字

为简单起见，让我们使用 R`stats`已安装并使用机器学习服务 （预览版） 的 Azure SQL 数据库默认情况下加载的包。 包中包含数百个常用统计任务，在它们之间的函数`rnorm`函数。 此函数将生成指定的数量的给定了标准偏差，表示使用正态分布的随机数字。

例如，下面的 R 代码返回 100 个数字平均数为 50，给定了标准差为 3。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

若要从 T-SQL 调用此行 R，运行`sp_execute_external_script`和 R 脚本参数，此类中添加此 R 函数：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

如果你想要更加轻松地生成一组不同的随机数字？

这很容易与 SQL 结合使用时。 定义从用户获取的参数，然后将这些自变量作为变量传递给 R 脚本的存储的过程。

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

- 第一行定义每个 SQL 输入参数执行存储的过程时所需的。

- 开头的行`@params`定义使用的 R 代码，以及相应的 SQL 数据类型的所有变量。

- 紧跟在行映射到相应的 R 变量名称的 SQL 参数名称。

既然您已将 R 函数包装在存储过程中，可以轻松地调用该函数并传入不同值，如下：

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>使用 R 实用工具函数进行故障排除

`utils`默认情况下，安装包提供了各种用于调查当前 R 环境的实用工具函数。 这些函数可以是你在 R 代码执行 SQL 中和在环境外的方法中发现差异时很有用。 例如，可以使用 R`memory.limit()`函数来获取当前 R 环境的内存。

因为`utils`包已安装但未加载默认情况下，必须使用`library()`函数首先加载。

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
> 许多用户喜欢使用的系统计时函数在 R 中，如`system.time`和`proc.time`，以捕获 R 进程使用的时间和分析性能问题。
