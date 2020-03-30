---
title: 了解 Azure 数据湖分析 U-SQL 开发人员的 Apache Spark 代码概念。
description: 本文介绍了 Apache Spark 概念，以帮助 U-SQL 开发人员了解 Spark 代码概念。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424002"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>了解 U-SQL 开发人员的 Apache Spark 代码

本节提供有关将 U-SQL 脚本转换为 Apache Spark 的高级指导。

- 它从[比较两种语言的处理范式](#understand-the-u-sql-and-spark-language-and-processing-paradigms)开始
- 提供有关如何操作的提示：
   - [转换脚本](#transform-u-sql-scripts)，包括 U-SQL 的[行集表达式](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions)
   - [.NET 代码](#transform-net-code)
   - [数据类型](#transform-typed-values)
   - [目录对象](#transform-u-sql-catalog-objects)。

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>了解 U-SQL 和 Spark 语言和处理范例

在开始将 Azure 数据湖分析的 U-SQL 脚本迁移到 Spark 之前，了解两个系统的一般语言和处理理念非常有用。

U-SQL 是一种类似于 SQL 的声明性查询语言，它使用数据流范例，并允许您轻松嵌入和扩展以 .NET（例如 C#）、Python 和 R 编写的用户代码。用户扩展可以实现简单的表达式或用户定义的函数，但还可以为用户提供实现所谓的用户定义的运算符的能力，这些运算符实现自定义运算符以执行行集级别转换、提取和写入输出。

Spark 是一个横向扩展框架，在 Scala、Java、Python、.NET 等中提供多种语言绑定，在这些绑定中，您主要用这些语言之一编写代码，创建称为弹性分布式数据集 （RDD）、数据帧和数据集的数据抽象以及然后使用类似 LINQ 的域特定语言 （DSL） 来转换它们。 它还提供 SparkSQL 作为数据帧和数据集抽象的声明性子语言。 DSL 提供两类操作，即转换和操作。 将转换应用于数据抽象不会执行转换，而是构建将提交用于操作进行评估的执行计划（例如，将结果写入临时表或文件，或打印结果）。

因此，将 U-SQL 脚本转换为 Spark 程序时，您必须决定要使用哪种语言至少生成数据帧抽象（这是当前最常用的数据抽象），以及是否要编写声明性使用 DSL 或 SparkSQL 的数据流转换。 在某些更复杂的情况下，您可能需要将 U-SQL 脚本拆分为 Spark 序列以及使用 Azure 批处理或 Azure 函数实现的其他步骤。

此外，Azure 数据湖分析在无服务器作业服务环境中提供 U-SQL，而 Azure 数据块和 Azure HDInsight 都以群集服务的形式提供 Spark。 在转换应用程序时，必须考虑现在创建、调整大小、缩放和停用群集的影响。

## <a name="transform-u-sql-scripts"></a>转换 U-SQL 脚本

U-SQL 脚本遵循以下处理模式：

1. 数据从非结构化文件、使用`EXTRACT`语句、位置或文件集规范、内置或用户定义的提取器和所需架构或 U-SQL 表（托管表或外部表）读取。 它表示为行集。
2. 行集在多个 U-SQL 语句中转换，这些语句将 U-SQL 表达式应用于行集并生成新的行集。
3. 最后，生成的行集使用指定位置和内置输出器或`OUTPUT`用户定义的输出器的语句输出到任一文件中，或者输出到 U-SQL 表中。

脚本被懒惰地计算，这意味着每个提取和转换步骤都组成一个表达式树并全局计算（数据流）。

Spark 程序类似，因为您将使用 Spark 连接器读取数据并创建数据帧，然后使用 LINQ 样的 DSL 或 SparkSQL 在数据帧上应用转换，然后将结果写入文件、临时 Spark 表，某些编程语言类型，或控制台。

## <a name="transform-net-code"></a>转换 .NET 代码

U-SQL 的表达式语言是 C#，它提供了多种方法来扩展自定义 .NET 代码。

由于 Spark 目前不支持本机执行 .NET 代码，因此您必须将表达式重写为等效的 Spark、Scala、Java 或 Python 表达式，或者找到调用 .NET 代码的方法。 如果脚本使用 .NET 库，则具有以下选项：

- 将 .NET 代码转换为 Scala 或 Python。
- 将 U-SQL 脚本拆分为几个步骤，其中使用 Azure 批处理过程应用 .NET 转换（如果可以获得可接受的比例）
- 使用开放源码中提供的名为 Moebius 的 .NET 语言绑定。 此项目未处于受支持状态。

在任何情况下，如果您的 U-SQL 脚本中具有大量的 .NET 逻辑，请通过 Microsoft 帐户代表与我们联系，以进行进一步的指导。

以下详细信息适用于 U-SQL 脚本中 .NET 和 C# 用法的不同情况。

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>转换标量内联 U-SQL C++ 表达式

U-SQL 的表达式语言是 C#。 许多标量内联 U-SQL 表达式是本机实现的，以提高性能，而更复杂的表达式可以通过调用 .NET 框架来执行。

Spark 有自己的标量表达式语言（作为 DSL 的一部分或 SparkSQL 中的一部分），并允许调用以托管语言编写的用户定义的函数。

如果在 U-SQL 中具有标量表达式，则应首先找到最适当的本机理解的 Spark 标量表达式以获得最佳性能，然后将其他表达式映射到您选择的 Spark 托管语言的用户定义的函数中。

请注意，.NET 和 C# 的类型语义与 Spark 托管语言和 Spark 的 DSL 不同。 有关类型系统差异的更多详细信息，请参阅[下文](#transform-typed-values)。

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>转换用户定义的标量 .NET 函数和用户定义的聚合器

U-SQL 提供了调用任意标量 .NET 函数和调用以 .NET 编写的用户定义的聚合器的方法。

Spark 还支持用户定义的函数和用户定义的聚合器，这些聚合器使用大多数托管语言编写，这些语言可以从 Spark 的 DSL 和 SparkSQL 调用。

### <a name="transform-user-defined-operators-udos"></a>转换用户定义的运算符 （UDO）

U-SQL 提供了几类用户定义的运算符 （UDO），如提取器、输出器、缩减器、处理器、应用程序器和组合器，这些运算符可以在 .NET（在某种程度上- 在 Python 和 R 中）编写。

Spark 不为操作员提供相同的扩展模型，但对某些运算符具有等效功能。

与提取器和输出器等效的火花是火花连接器。 对于许多 U-SQL 提取器，您可能会在 Spark 社区中找到等效连接器。 对于其他人，您必须编写自定义连接器。 如果 U-SQL 提取器很复杂，并且使用多个 .NET 库，则最好在 Scala 中构建一个连接器，该连接器使用 Interop 调用执行数据实际处理的 .NET 库。 在这种情况下，您必须将 .NET Core 运行时部署到 Spark 群集，并确保引用的 .NET 库符合 .NET 标准 2.0 标准。

其他类型的 U-SQL UDO 需要使用用户定义的函数和聚合器以及语义上适当的 Spark DLS 或 SparkSQL 表达式重写。 例如，处理器可以映射到各种 UDF 调用的 SELECT，打包为一个函数，该函数将数据框作为参数并返回数据框。

### <a name="transform-u-sqls-optional-libraries"></a>转换 U-SQL 的可选库

U-SQL 提供了一组可选和演示库，提供[Python](data-lake-analytics-u-sql-python-extensions.md)Python、R、JSON、XML、AVRO[支持](https://github.com/Azure/usql/tree/master/Examples/DataFormats)和一些[认知服务功能](data-lake-analytics-u-sql-cognitive.md)。 [R](data-lake-analytics-u-sql-r-extensions.md)

Spark 分别提供自己的 Python 和 R 集成、pySpark 和 SparkR，并提供用于读取和写入 JSON、XML 和 AVRO 的连接器。

如果您需要转换引用认知服务库的脚本，我们建议您通过 Microsoft 帐户代表与我们联系。

## <a name="transform-typed-values"></a>变换类型化值

由于 U-SQL 的类型系统基于 .NET 类型系统，Spark 有自己的类型系统，受主机语言绑定的影响，因此您必须确保操作的类型接近某些类型，因此类型范围、精度和/或比例可能略有不同。 此外，U-SQL 和`null`Spark 对待值的方式也不同。

### <a name="data-types"></a>数据类型

下表为给定的 USQL 类型提供了 Spark、Scala 和 PySpark 中的等效类型。

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

有关详细信息，请参阅：

- [org.apache.spark.sql.type](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [火花 SQL 和数据帧类型](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala 值类型](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.type](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL 的处理

在 Spark 中，每个默认值的类型允许 NULL 值，而在 U-SQL 中，显式将标量、非对象标记为空。 虽然 Spark 允许您将列定义为不可取消，但它不会强制执行约束，[并可能导致错误的结果](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)。

在 Spark 中，NULL 表示该值未知。 Spark NULL 值不同于任何值，包括其自身。 两个 Spark NULL 值之间的比较，或 NULL 值和任何其他值之间的比较，返回未知，因为每个 NULL 的值未知。  

此行为不同于 U-SQL，U-SQL 遵循 C#`null`语义，其中与任何值不同，但等于自身。  

因此，使用的`SELECT``WHERE column_name = NULL`SparkSQL 语句返回零行，即使 中`column_name`存在 NULL 值也是如此，而在 U-SQL`column_name`中，它将`null`返回设置为 的行。 同样，使用`SELECT``WHERE column_name != NULL`Spark 语句返回零行，即使 中`column_name`存在非空值也是如此，而在 U-SQL 中，它将返回具有非空的行。 因此，如果想要 U-SQL 空检查语义，则应分别使用[isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull)和[isnotnull（](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull)或其 DSL 等效项）。

## <a name="transform-u-sql-catalog-objects"></a>转换 U-SQL 目录对象

一个主要区别是 U-SQL 脚本可以使用其目录对象，其中许多对象没有直接的 Spark 等效项。

Spark 确实支持 Hive Meta 存储概念（主要是数据库和表），因此您可以将 U-SQL 数据库和架构映射到 Hive 数据库，将 U-SQL 表映射到 Spark 表（请参阅[移动存储在 U-SQL 表中的数据](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)），但它不支持视图、表值函数 （TVF）、存储过程、U-SQL 程序集、外部数据源等。

U-SQL 代码对象（如视图、TVF、存储过程和程序集）可以通过 Spark 中的代码函数和库进行建模，并使用宿主语言的功能和程序抽象机制（例如，通过导入）进行引用Python 模块或引用 Scala 函数）。

如果 U-SQL 目录已用于跨项目和团队共享数据和代码对象，则必须使用等效的共享机制（例如，用于共享代码对象的 Maven）。

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>转换 U-SQL 行集表达式和基于 SQL 的标量表达式

U-SQL 的核心语言正在转换行集，并且基于 SQL。 以下是 U-SQL 中提供的最常见行集表达式的非详尽列表：

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`[聚合]`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`表达式
- `CROSS`/`OUTER``APPLY`表达式
- `PIVOT`/`UNPIVOT`表达 式
- `VALUES`排集构造函数

- 设置表达式`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

此外，U-SQL 提供了各种基于 SQL 的标量表达式，例如

- `OVER`窗口表达式
- 各种内置聚合器和排名函数（`SUM`等） `FIRST`
- 一些最熟悉的 SQL 标量表达式： `CASE`、 `LIKE`、`NOT` `IN`（ `AND` `OR` ） 等。

Spark 以 DSL 和 SparkSQL 形式为大多数这些表达式提供了等效表达式。 Spark 中本机不支持的某些表达式必须使用本机 Spark 表达式和语义等效模式的组合进行重写。 例如，`OUTER UNION`必须转化为预测和联合的等效组合。

由于对 NULL 值的处理不同，如果比较的两列都包含 NULL 值，则 U-SQL 联接始终匹配一行，而 Spark 中的联接将不匹配此类列，除非添加显式空检查。

## <a name="transform-other-u-sql-concepts"></a>转换其他 U-SQL 概念

U-SQL 还提供各种其他功能和概念，例如针对 SQL Server 数据库、参数、标量和 lambda 表达式变量、系统变量和`OPTION`提示的联合查询。

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>针对 SQL Server 数据库/外部表的联合查询

U-SQL 提供数据源和外部表，以及针对 Azure SQL 数据库的直接查询。 虽然 Spark 不提供相同的对象抽象，但它为[Azure SQL 数据库提供了可用于](../sql-database/sql-database-spark-connector.md)查询 SQL 数据库的 Spark 连接器。

### <a name="u-sql-parameters-and-variables"></a>U-SQL 参数和变量

参数和用户变量在 Spark 及其托管语言中具有等效的概念。

例如，在 Scala 中，可以使用`var`关键字定义变量：

```
var x = 2 * 3;
println(x)
```

U-SQL 的系统变量（以`@@`开头的变量）可以分为两类：

- 可设置为特定值以影响脚本行为的可设置系统变量
- 查询系统和作业级别信息的信息系统变量

大多数可设置系统变量在 Spark 中没有直接等效项。 某些信息系统变量可以通过在作业执行期间将信息作为参数传递来建模，而其他变量在 Spark 的托管语言中可能具有等效函数。

### <a name="u-sql-hints"></a>U-SQL 提示

U-SQL 提供了几种语法方法，可向查询优化器和执行引擎提供提示：  

- 设置 U-SQL 系统变量
- 与`OPTION`行集表达式关联的子句，用于提供数据或计划提示
- 联接表达式语法中的联接提示（例如， `BROADCASTLEFT`

Spark 基于成本的查询优化器具有提供提示和调整查询性能的功能。 请参阅相应的文档。

## <a name="next-steps"></a>后续步骤

- [了解 U-SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)
