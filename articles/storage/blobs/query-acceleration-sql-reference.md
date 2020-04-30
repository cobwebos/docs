---
title: 查询加速 SQL 语言参考（预览版）
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81772114"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>查询加速 SQL 语言参考（预览版）

查询加速支持使用类似于 SQL 的语言来表示对 blob 内容的查询。  查询加速 SQL 方言是 ANSI SQL 的一个子集，其中包含有限的一组受支持的数据类型、运算符等，但它还在 ANSI SQL 上展开以支持对层次结构半结构化数据格式（如 JSON）的查询。 

> [!NOTE]
> 查询加速功能处于公共预览阶段，在加拿大中部和法国中部区域提供。 若要查看限制，请参阅[已知问题](data-lake-storage-known-issues.md)一文。 若要注册预览版，请参阅[此窗体](https://aka.ms/adls/qa-preview-signup)。 

## <a name="select-syntax"></a>选择语法

查询加速支持的唯一 SQL 语句是 SELECT 语句。 此示例返回表达式为其返回 true 的每一行。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

对于 CSV 格式的数据，*表*必须是`BlobStorage`。  这意味着，将针对 REST 调用中指定的任何 blob 运行查询。
对于 JSON 格式的数据，*表*是 "表描述符"。   请参阅本文的[表描述符](#table-descriptors)部分。

在下面的示例中，对于 WHERE*表达式*为其返回 true 的每一行，此语句将返回一个新行，该行是从计算每个投影表达式计算得出的。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

下面的示例在*表达式*返回 true 的每个行上返回聚合计算（例如，特定列的平均值）。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

下面的示例为拆分 CSV 格式的 blob 返回合适的偏移量。  请参阅本文的[sys.databases](#sys-split)部分。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>数据类型

|数据类型|说明|
|---------|-------------------------------------------|
|INT      |64 位带符号整数。                     |
|FLOAT    |64位（"双精度"）浮点。|
|STRING   |可变长度的 Unicode 字符串。            |
|TIMESTAMP|时间点。                           |
|BOOLEAN  |True 或 False。                             |

从 CSV 格式的数据中读取值时，所有值都作为字符串读取。  使用强制转换表达式可以将字符串值转换为其他类型。  根据上下文，值可以隐式强制转换为其他类型。 有关详细信息，请参阅[数据类型优先级（transact-sql）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)。

## <a name="expressions"></a>表达式

### <a name="referencing-fields"></a>引用字段

对于 JSON 格式的数据或带有标题行的 CSV 格式的数据，可以按名称引用字段。  字段名称可以带引号或不带引号。 带引号的字段名用双引号（"）引起来，可包含空格，并区分大小写。  未加引号的字段名称不区分大小写，并且不能包含任何特殊字符。

在 CSV 格式的数据中，字段也可以按序号引用，并以下划线（_）字符作为前缀。  例如，第一个字段可能被引用为 _1，或者第11个字段可以作为 _11 引用。  引用字段按序号适用于不包含标题行的 CSV 格式的数据，在这种情况下，引用特定字段的唯一方法是按序号。

### <a name="operators"></a>运算符

支持以下标准 SQL 运算符：

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

如果运算符左侧和右侧的数据类型不同，则将根据此处指定的规则执行自动转换：[数据类型优先级（transact-sql）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)。

查询加速 SQL 语言仅支持在本文中讨论的数据类型的一小部分。  请参阅本文的[数据类型](#data-types)部分。

### <a name="casts"></a>转换

查询加速 SQL 语言支持强制转换运算符，具体取决于此处的规则：[数据类型转换（数据库引擎）](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017)。  

查询加速 SQL 语言仅支持在本文中讨论的数据类型的一小部分。  请参阅本文的[数据类型](#data-types)部分。

### <a name="string-functions"></a>字符串函数

查询加速 SQL 语言支持以下标准 SQL 字符串函数：

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

下面是几个示例：

|函数|示例|结果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函数可帮助你搜索模式。 下面是几个使用[LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15)函数搜索数据字符串``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``的示例。

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

目前，我们转换了[标准 IS08601 的所有日期格式](https://www.w3.org/TR/NOTE-datetime)。 

#### <a name="date_add-function"></a>DATE_ADD 函数

查询加速 SQL 语言支持``DATE_ADD``函数的年、月、日、小时、分钟和秒。

示例：

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF 函数

查询加速 SQL 语言支持``DATE_DIFF``函数的年、月、日、小时、分钟和秒。

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>提取函数

对于超出``DATE_ADD``函数支持的日期部分，查询加速 SQL 语言支持 timezone_hour 和 timezone_minute 日期部分。

示例：

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 函数

示例：

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

下表描述了可用于指定``TO_STRING``函数的输出格式的字符串。

|格式字符串    |输出                               |
|-----------------|-------------------------------------|
|yy               |年份采用2位格式–1999作为 "99"|
|y                |年份采用4位格式               |
|yyyy             |年份采用4位格式               |
|M                |每年的第几个月–1                    |
|MM               |零填充月份–01               |
|MMM              |缩写. 每月的某一日            |
|MMMM             |全月–可能                      |
|d                |每月的某一日（1-31）                  |
|dd               |零个填充的日期（01-31）     |
|a                |AM 或 PM                             |
|h                |一天中的小时（1-12）                   |
|hh               |零填充小时 od day （01-12）     |
|H                |一天中的小时（0-23）                   |
|HH               |一天中的零个填充小时（00-23）      |
|m                |分钟分钟（0-59）                |
|mm               |零填充分钟（00-59）           |
|s                |分钟的秒数（0-59）             |
|ss               |填充的秒数（00-59）          |
|S                |秒的小数部分（0.1-0.9）        |
|SS               |秒的小数部分（0.01-0.99）      |
|SSS              |秒的小数部分（0.001-0.999）    |
|X                |偏移量（小时）                      |
|XX 或 XXXX       |以小时和分钟为单位的偏移量（+ 0430）  |
|XXX 或 XXXXX     |以小时和分钟为单位的偏移量（-07:00） |
|x                |偏移量（小时）（7）                  |
|xx 或 xxxx       |偏移量（小时和分钟）（+ 0530）    |
|Xxx 或 xxxxx     |偏移量（小时和分钟）（+ 05:30）   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 函数

仅支持 IS08601 格式。

示例：

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> 你还可以使用``UTCNOW``函数来获取系统时间。


## <a name="aggregate-expressions"></a>聚合表达式

SELECT 语句可能包含一个或多个投影表达式或单个聚合表达式。  支持以下聚合表达式：

|表达式|说明|
|--|--|
|[COUNT （\*）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回与谓词表达式匹配的记录数。|
|[COUNT （expression）](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |返回表达式为非 null 的记录数。|
|[AVERAGE （expression）](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |返回 expression 的非 null 值的平均值。|
|[MIN （expression）](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |返回 expression 的最小非 null 值。|
|[MAX （expression](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)）    |返回表达式的最大非 null 值。|
|[SUM （expression）](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |返回表达式的所有非 null 值的总和。|

### <a name="missing"></a>缺少

``IS MISSING``运算符是查询加速 SQL 语言支持的唯一非标准。  对于 JSON 数据，如果特定输入记录中缺少某个字段，则表达式字段``IS MISSING``的计算结果为布尔值 true。

<a id="table-descriptors" />

## <a name="table-descriptors"></a>表描述符

对于 CSV 数据，表名称始终`BlobStorage`为。  例如：

```sql
SELECT * FROM BlobStorage
```

对于 JSON 数据，可以使用其他选项：

```sql
SELECT * FROM BlobStorage[*].path
```

这允许对 JSON 数据的子集进行查询。

对于 JSON 查询，可以在 FROM 子句中提到该路径。 这些路径将帮助分析 JSON 数据的子集。 这些路径可以引用 JSON 数组和对象值。

下面以更详细的示例来了解这一点。

下面是示例数据：

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

你可能只对上述数据的`warehouses` JSON 对象感兴趣。 `warehouses`对象是一个 JSON 数组类型，因此你可以在 FROM 子句中提到此类型。 示例查询如下所示。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

查询将获取所有字段，但仅选择纬度。

如果只想访问 JSON 对象的`dimensions`值，则可以在查询中使用引用该对象。 例如：

```sql
SELECT length FROM BlobStorage[*].dimensions
```

这还限制了对`dimensions`对象成员的访问。 如果要访问 json 字段的其他成员和 JSON 对象的内部值，则可以使用如下面的示例中所示的查询：

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage 和 BlobStorage [\*] 均引用整个对象。 但是，如果在 FROM 子句中有路径，则需要使用 BlobStorage [\*]. 路径

<a id="sys-split" />

## <a name="syssplit"></a>Sys. Split

这是 SELECT 语句的一种特殊形式，仅适用于 CSV 格式的数据。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

如果要成批下载并处理 CSV 数据记录，请使用此语句。 这样一来，就可以并行处理记录，而无需一次下载所有记录。 此语句不从 CSV 文件返回记录。 而是返回批大小的集合。 然后，可以使用每个批大小检索一批数据记录。 

使用*split_size*参数可指定每个批处理要包含的字节数。 例如，如果希望一次只处理 10 MB 的数据，则语句将如下所示： `SELECT sys.split(10485760)FROM BlobStorage`因为 10 MB 等于10485760字节。 每个批处理将包含尽可能多的记录。 

在大多数情况下，每个批的大小将略高于你指定的数字。 这是因为批处理不能包含部分记录。 如果批处理中的最后一条记录在阈值结束之前开始，则批处理将会更大，因此它可以包含完整记录。 最后一批的大小可能小于指定的大小。

>[!NOTE]
> Split_size 必须至少为 10 MB （10485760）。

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage 查询加速（预览版）](data-lake-storage-query-acceleration.md)
- [使用 Azure Data Lake Storage 查询加速来筛选数据（预览）](data-lake-storage-query-acceleration-how-to.md)

