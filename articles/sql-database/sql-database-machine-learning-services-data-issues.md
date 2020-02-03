---
title: 使用 R 和 SQL 数据类型和对象
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 了解如何使用机器学习服务（预览版）在 R 中使用 Azure SQL 数据库中的数据类型和数据对象，包括你可能会遇到的常见问题。
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
ms.openlocfilehash: 7dfd12729c5697d1935d098cbd4ed863a4551acd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719868"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>使用 Azure SQL 数据库中的 R 和 SQL 数据机器学习服务（预览版）

本文介绍了在[AZURE SQL 数据库中机器学习服务（与 R）](sql-database-machine-learning-services-overview.md)中的 r 和 SQL 数据库之间移动数据时可能会遇到的一些常见问题。 通过本练习所获得的经验可以为在自己的脚本中处理数据时提供必要的背景信息。

你可能会遇到的常见问题包括：

- 数据类型有时不匹配
- 可能会发生隐式转换
- 有时需要执行强制转换和转换操作
- R 和 SQL 使用不同的数据对象

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必备条件

- 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/)。

- 若要在这些练习中运行示例代码，必须先有一个启用了机器学习服务（使用 R）的 Azure SQL 数据库。 在发布公共预览版期间，Microsoft 会将你加入该版本并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

- 确保已安装了最新的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 你可以使用其他数据库管理或查询工具运行 R 脚本，但在本快速入门中，你将使用 SSMS。

## <a name="working-with-a-data-frame"></a>使用数据帧

当脚本从 R 返回 SQL 返回结果时，它必须将数据作为**数据帧**返回。 在脚本中生成的其他任何对象类型（不管是列表、因子、向量还是二进制数据）都必须转换为数据框架，这样才能将它输出为存储过程结果的一部分。 幸运的是，有多个 R 函数支持将其他对象更改为数据框架。 您甚至可以序列化二进制模型，并在数据帧中返回它，稍后将在本文中进行此操作。

首先，让我们试验一些基本的 R 对象-矢量、矩阵和列表，并了解如何转换到数据帧会更改传递到 SQL 的输出。

比较 R 中的这两个“Hello World”脚本。这两个脚本看上去几乎完全相同，但第一个脚本返回一个包含三个值的列，而第二个脚本则返回三个包含单个值的列。

**示例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**示例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

结果为何有这么大的差别？

使用 R `str()` 命令通常可以找到答案。 在 R 脚本中的任何位置添加函数 `str(object_name)` 可使指定 R 对象的数据架构作为信息性消息返回。 可以在 SSMS 的 "**消息**" 选项卡中查看消息。

若要确定示例1和示例2的原因，请在每个语句的 `@script` 变量定义的末尾插入行 `str(OutputDataSet)`，如下所示：

**添加了 str 函数的示例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**添加了 str 函数的示例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

现在，查看“消息”中的文本，了解输出为何不相同的原因。

**结果 - 示例 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**结果 - 示例 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

可以看到，对 R 语法进行轻微的更改会给结果的架构造成很大的影响。 对于所有详细信息，R 数据类型中的差异在[Hadley Wickham 的 "高级 R" 的 "](http://adv-r.had.co.nz)*数据结构*" 部分的详细信息中进行了介绍。

暂时，你只需在将 R 对象强制转换成数据框架时注意检查预期的结果。

> [!TIP]
> 你还可以使用 R 标识函数（如 `is.matrix``is.vector`）返回有关内部数据结构的信息。

## <a name="implicit-conversion-of-data-objects"></a>数据对象的隐式转换

如果两个数据对象具有相同的维数，或任何数据对象包含异类数据类型，则每个 R 数据对象都有其自己的规则，用于将值与其他数据对象结合使用。

例如，假设您要使用 R 执行矩阵乘法。您需要将包含三个值的单列矩阵乘以具有四个值的数组，并期望4x3 矩阵为结果。

首先，创建一个小型测试数据表。

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

现在，请运行以下脚本。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

在幕后，包含三个值的列将转换为单列矩阵。 由于矩阵只是 R 中数组的特殊表示形式，数组 `y` 将隐式强制转换为单列矩阵，使这两个参数相符。

**结果**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

但是，请注意更改数组 `y` 的大小时会发生什么情况。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

现在，R 返回单个值作为结果。

**结果**
    
|Col1|
|---|
|1542|

为什么? 在本例中，由于可以将这两个参数作为长度相同的向量进行处理，R 会以矩阵形式返回内积。  这是预期的行为，具体取决于线性代数的规则。 但是，如果下游应用程序需要输出架构永远不会发生更改，则可能会导致问题！

## <a name="merge-or-multiply-columns-of-different-length"></a>对不同长度的列进行合并或相乘

在处理不同大小的向量以及将这些类似于列的结构组合成数据框架方面，R 提供了很大的弹性。 向量列表可能看起来像表，但它们并不遵循控制数据库表的所有规则。

例如，以下脚本定义长度为 6 的数字数组，并将其存储在 R 变量 `df1` 中。 然后，将该数值数组与包含三（3）个值的 RTestData 表的整数组合，以使新的数据帧 `df2`。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

为了填充数据框架，R 将以所需的次数重复从 RTestData 检索的元素，以匹配数组 `df1` 中的元素数目。

**结果**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

请记住，数据帧只是看起来像是一个表，但实际上是一个向量列表。

## <a name="cast-or-convert-sql-data"></a>强制转换或转换 SQL 数据

R 和 SQL 不使用相同的数据类型，因此，在 SQL 中运行查询以获取数据，然后将其传递给 R 运行时，通常会发生某种类型的隐式转换。 在将数据从 R 返回到 SQL 时，会进行另一组转换。

- SQL 将数据从查询推送到 R 进程，并将其转换为内部表示形式以提高效率。
- R 运行时将数据加载到 data.frame 变量中，并对数据执行其自身的操作。
- 数据库引擎使用安全的内部连接将数据返回到 SQL，并以 SQL 数据类型表示数据。
- 您可以通过使用客户端或网络库（可以发出 SQL 查询并处理表格数据集）连接到 SQL 来获取数据。 此客户端应用程序可能会通过其他方式影响数据。

若要了解这种处理方式的工作原理，请针对 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 数据仓库运行如下所示的查询。 此视图返回用于创建预测的销售数据。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 可以使用任何版本的 AdventureWorks，也可以使用自己的数据库创建不同的查询。 要点是尝试处理包含文本、日期时间和数字值的某些数据。

现在，请尝试使用此查询作为存储过程的输入。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

如果遇到错误，可能需要对查询文本进行一些编辑。 例如，必须用两组单引号将 WHERE 子句中的字符串谓词括起来。

正常运行查询后，请查看 `str` 函数的结果，了解 R 如何处理输入数据。

**结果**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- 日期时间列已使用 R 数据类型 **POSIXct** 进行处理。
- 文本列“ProductSeries”已标识为因子，意味着它是一个分类变量。 默认情况下，字符串值将作为因子处理。 如果将某个字符串传递给 R，该字符串将转换为整数供内部使用，然后映射回到输出中的字符串。

## <a name="summary"></a>总结

即使是这些简短的示例，你也可以看出将 SQL 查询作为输入进行传递时需要检查数据转换的影响。 由于 R 不支持某些 SQL 数据类型，因此请考虑使用以下方法来避免错误：

- 在将数据传递给 R 代码之前，请预先测试数据并验证架构中可能会造成问题的列或值。
- 在输入数据源中单独指定列而不要使用 `SELECT *`，并知道如何处理每个列。
- 准备输入数据时请根据需要执行显式强制转换，避免出现意外。
- 避免传递导致错误以及对建模无用的数据列（例如 GUIDS 或 rowguids）。

有关支持的和不支持的 R 数据类型的详细信息，请参阅[r 库和数据类型](/sql/advanced-analytics/r/r-libraries-and-data-types)。
