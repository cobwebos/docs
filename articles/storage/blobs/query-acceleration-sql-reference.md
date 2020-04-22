---
title: 查询加速 SQL 语言参考（预览）
titleSuffix: Azure Storage
description: 了解如何使用查询加速 sql 语法。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772114"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>查询加速 SQL 语言参考（预览）

查询加速支持类似 ANSI SQL 的语言，用于对 blob 内容表示查询。  查询加速 SQL 方言是 ANSI SQL 的子集，具有有限的受支持数据类型、运算符等，但它也扩展了 ANSI SQL 以支持对分层半结构化数据格式（如 JSON）的查询。 

> [!NOTE]
> 查询加速功能处于公共预览版中，并且在加拿大中部和法国中部区域可用。 要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 要在预览版中注册，请参阅[此窗体](https://aka.ms/adls/qa-preview-signup)。 

## <a name="select-syntax"></a>选择语法

查询加速度支持的唯一 SQL 语句是 SELECT 语句。 此示例返回表达式返回 true 的每一行。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

对于 CSV 格式的数据，*表*必须为`BlobStorage`。  这意味着查询将针对 REST 调用中指定的任何 Blob 运行。
对于 JSON 格式的数据，*表*是"表描述符"。   请参阅本文的[表描述符](#table-descriptors)部分。

在下面的示例中，对于 WHERE*表达式*返回 true 的每个行，此语句将返回一个新行，该行是从计算每个投影表达式时产生的。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

下面的示例返回*表达式*返回 true 的每个行上的聚合计算（例如：特定列的平均值）。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

下面的示例返回用于拆分 CSV 格式的 blob 的适当偏移量。  请参阅本文的[Sys.拆分](#sys-split)部分。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>数据类型

|数据类型|说明|
|---------|-------------------------------------------|
|INT      |64 位带符号整数。                     |
|FLOAT    |64 位（"双精度"）浮点。|
|STRING   |可变长度 Unicode 字符串。            |
|TIMESTAMP|时间点。                           |
|BOOLEAN  |True 或 False。                             |

从 CSV 格式的数据读取值时，所有值都读为字符串。  可以使用 CAST 表达式将字符串值转换为其他类型的字符串值。  根据上下文，可以隐式地转换为其他类型的值。 有关详细信息，请参阅[数据类型优先级（转用 SQL）。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

## <a name="expressions"></a>表达式

### <a name="referencing-fields"></a>引用字段

对于 JSON 格式的数据，或者具有标头行的 CSV 格式数据，字段可以按名称引用。  字段名称可以引用或取消引用。 引用的字段名称以双引号字符 （））括起来，可能包含空格，并且区分大小写。  未引用的字段名称不区分大小写，可能不包含任何特殊字符。

在 CSV 格式的数据中，字段也可以由具有下划线 （*） 字符的预缀引用。  例如，第一个字段可以引用为 |1，或者第 11 个字段可以引用为 |11。  通过处理形式引用字段对于不包含标头行的 CSV 格式数据很有用，在这种情况下，引用特定字段的唯一方法是按单位。

### <a name="operators"></a>运算符

支持以下标准 SQL 运算符：

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

如果运算符的左侧和右侧的数据类型不同，则将根据此处指定的规则执行自动转换：[数据类型优先级（处理-SQL）。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

查询加速 SQL 语言仅支持本文中讨论的数据类型的一小部分。  请参阅本文的["数据类型"](#data-types)部分。

### <a name="casts"></a>转换

查询加速 SQL 语言支持 CAST 运算符，根据此处的规则：[数据类型转换（数据库引擎）。](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017)  

查询加速 SQL 语言仅支持本文中讨论的数据类型的一小部分。  请参阅本文的["数据类型"](#data-types)部分。

### <a name="string-functions"></a>字符串函数

查询加速 SQL 语言支持以下标准 SQL 字符串函数：

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

下面是一些示例：

|函数|示例|结果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函数可帮助您搜索模式。 下面是一些使用[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函数来搜索数据字符串``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``的示例。

|查询|示例|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>日期函数

支持以下标准 SQL 日期函数：

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

目前，我们转换标准[IS08601的所有日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD功能

查询加速 SQL 语言支持函数的``DATE_ADD``年、月、日、小时、分钟、秒。

示例：

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF功能

查询加速 SQL 语言支持函数的``DATE_DIFF``年、月、日、小时、分钟、秒。

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>提取功能

对于``DATE_ADD``函数支持的日期部件以外的 EXTRACT，查询加速 SQL 语言支持timezone_hour和timezone_minute作为日期部分。

示例：

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING功能

示例：

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

此表描述可用于指定函数的输出格式的``TO_STRING``字符串。

|格式字符串    |输出                               |
|-----------------|-------------------------------------|
|yy               |2 位格式的年份 – 1999 年为"99"|
|y                |4 位格式的年份               |
|yyyy             |4 位格式的年份               |
|M                |一年月 = 1                    |
|MM               |零填充月 = 01               |
|MMM              |Abbr. 一年月 - JAN            |
|MMMM             |整个月数 = 5 月                      |
|d                |月日 （1-31）                  |
|dd               |零填充月日 （01-31）     |
|a                |上午或下午                             |
|h                |一天中小时 （1-12）                   |
|hh               |零填充小时 od 天 （01-12）     |
|H                |一天中小时 （0-23）                   |
|HH               |一天零加时 （00-23）      |
|m                |小时分钟 （0-59）                |
|mm               |零填充分钟 （00-59）           |
|s                |第二分钟 （0-59）             |
|ss               |零填充秒数 （00-59）          |
|S                |秒数分数 （0.1-0.9）        |
|SS               |秒数分数 （0.01-0.99）      |
|Sss              |秒数分数 （0.001-0.999）    |
|X                |以小时表示偏移                      |
|XX 或 XXXX       |偏移（以小时和分钟表示 ）（+0430）  |
|XXX 或 XXXXX     |偏移（小时和分钟）（-07：00） |
|x                |偏移（小时数）（7）                  |
|xx 或 xxx       |偏移（小时和分钟）（+0530）    |
|Xxx 或 xxxxxx     |偏移（小时和分钟）（+05：30）   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP功能

仅支持 IS08601 格式。

示例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 您还可以使用 函数``UTCNOW``获取系统时间。


## <a name="aggregate-expressions"></a>聚合表达式

SELECT 语句可以包含一个或多个投影表达式或单个聚合表达式。  支持以下聚合表达式：

|表达式|说明|
|--|--|
|[计数（\*）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回与谓词表达式匹配的记录数。|
|[COUNT（表达式）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回表达式为非空的记录数。|
|[平均（表达式）](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |返回表达式的非空值的平均值。|
|[MIN（表达式）](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |返回表达式的最小非空值。|
|[最大（表达式](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)）    |返回表达式的最大非空值。|
|[SUM（表达式）](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |返回表达式的所有非空值的总和。|

### <a name="missing"></a>失踪

运算符``IS MISSING``是查询加速 SQL 语言支持的唯一非标准。  对于 JSON 数据，如果特定输入记录中缺少一个字段，则表达式字段``IS MISSING``将计算为布尔值 true。

<a id="table-descriptors" />

## <a name="table-descriptors"></a>表描述符

对于 CSV 数据，表名称始终`BlobStorage`为 。  例如：

```sql
SELECT * FROM BlobStorage
```

对于 JSON 数据，还有其他选项可用：

```sql
SELECT * FROM BlobStorage[*].path
```

这允许查询 JSON 数据的子集。

对于 JSON 查询，您可以在 FROM 子句的一部分中提及路径。 这些路径将有助于分析 JSON 数据的子集。 这些路径可以引用 JSON 数组和对象值。

让我们举一个例子来更详细地理解这一点。

这是我们的示例数据：

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

您可能只对上述数据的`warehouses`JSON 对象感兴趣。 该`warehouses`对象是 JSON 数组类型，因此您可以在 FROM 子句中提及这一点。 您的示例查询可以如下所示。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

查询获取所有字段，但仅选择纬度。

如果只想访问`dimensions`JSON 对象值，可以使用查询中引用该对象。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

这还会限制您对`dimensions`对象成员的访问。 如果要访问 JSON 字段的其他成员和 JSON 对象的内部值，则可以使用查询，如以下示例所示：

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> Blob 存储和 Blob\*存储* 都引用整个对象。 但是，如果在 FROM 子句中有一个路径，则需要使用 BlobStorage]\*[路径]

<a id="sys-split" />

## <a name="syssplit"></a>系统拆分

这是 SELECT 语句的一种特殊形式，仅适用于 CSV 格式的数据。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

如果要下载，然后分批处理 CSV 数据记录，请使用此语句。 这样，您可以并行处理记录，而无需一次下载所有记录。 此语句不会从 CSV 文件返回记录。 相反，它返回批处理大小的集合。 然后，可以使用每个批处理大小检索一批数据记录。 

使用*split_size*参数指定您希望每个批处理包含的字节数。 例如，如果一次只想处理 10 MB 的数据，则语句如下所示：`SELECT sys.split(10485760)FROM BlobStorage`因为 10 MB 等于 10，485，760 字节。 每个批处理将包含尽可能多的记录，以适应这些 10 MB。 

在大多数情况下，每个批次的大小将略高于您指定的数字。 这是因为批处理不能包含部分记录。 如果批处理中的最后一个记录在阈值结束之前开始，则批处理将更大，以便它可以包含完整的记录。 最后一个批处理的大小可能小于您指定的大小。

>[!NOTE]
> split_size必须至少为 10 MB （10485760）。

## <a name="see-also"></a>另请参阅

- [Azure 数据湖存储查询加速（预览）](data-lake-storage-query-acceleration.md)
- [使用 Azure 数据湖存储查询加速筛选数据（预览）](data-lake-storage-query-acceleration-how-to.md)

