---
title: 映射数据流脚本
description: 数据工厂数据流脚本代码隐藏语言概述
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 8310c34e06d52dc12af42f8bc33f4a4d7e99d68d
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598095"
---
# <a name="data-flow-script-dfs"></a> (DFS) 的数据流脚本

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

 (DFS) 的数据流脚本是用于执行映射数据流中包含的转换的基本元数据，类似于编码语言。 每个转换均由一系列属性表示，这些属性提供正确运行作业所需的信息。 通过单击浏览器 UI 顶部功能区上的 "脚本" 按钮，可从 ADF 查看并编辑该脚本。

![脚本按钮](media/data-flow/scriptbutton.png "脚本按钮")

例如， `allowSchemaDrift: true,` 在源转换中，会告知服务将源数据集中的所有列包含在数据流中，即使它们未包含在架构投影中也是如此。

## <a name="use-cases"></a>用例
DFS 由用户界面自动生成。 您可以单击 "脚本" 按钮，查看并自定义该脚本。 你还可以在 ADF UI 外生成脚本，然后将其传递到 PowerShell cmdlet。 调试复杂数据流时，您可能会发现扫描脚本代码隐藏而不是扫描流的 UI 图形表示形式会更容易。

下面是几个示例用例：
- 以编程方式生成非常相似的多个数据流，即 "标记出" 数据流。
- 难以在 UI 中管理或导致验证问题的复杂表达式。
- 调试和更好地了解在执行过程中返回的各种错误。

在生成要用于 PowerShell 或 API 的数据流脚本时，必须将格式化文本折叠为一行。 您可以保留制表符和换行符作为转义符。 但必须对文本进行格式化，才能容纳到 JSON 属性中。 下面的脚本编辑器 UI 上有一个按钮，该按钮将脚本的格式设置为一行。

![“复制”按钮](media/data-flow/copybutton.png "“复制”按钮")

## <a name="how-to-add-transforms"></a>如何添加转换
添加转换需要三个基本步骤：添加核心转换数据、重新路由输入流，然后重新路由输出流。 在示例中，这种方法很容易看出。
假设我们从一个简单的源来接收数据流，如下所示：

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

如果我们决定添加派生转换，首先需要创建核心转换文本，其中包含一个简单的表达式用于添加一个名为的新大写列 `upperCaseTitle` ：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

接下来，我们获取现有 DFS 并添加转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

现在，我们会通过标识在这种情况下要使新转换 (的转换来重新路由传入流， `source1`) 并将流的名称复制到新的转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最后，我们要确定我们希望在此新转换后出现的转换，并将其输入流替换 (在这种情况下， `sink1`) 并将输出流名称替换为新的转换：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS 基础知识
DFS 由一系列相互连接的转换组成，其中包括源、接收器，以及可添加新列、筛选数据和联接数据等的其他各种转换。 通常情况下，使用一个或多个源开始的脚本会执行许多转换，并以一个或多个接收器结束。

所有源都具有相同的基本结构：
```
source(
  source properties
) ~> source_name
```

例如，具有三列 (movieId、title、流派) 的简单源将为：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

源以外的所有转换都具有相同的基本结构：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，使用列 (标题的简单派生转换) 并使用大写形式覆盖它，如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

无架构的接收器只是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>脚本代码段

脚本代码片段是可共享的数据流脚本代码，可用于跨数据流进行共享。 下面的视频介绍了如何使用脚本片段，并使用数据流脚本将部分脚本复制并粘贴到数据流关系图后面：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>聚合汇总统计信息
将聚合转换添加到名为 "SummaryStats" 的数据流中，然后将此代码粘贴到脚本中的聚合函数中，并替换现有的 SummaryStats。 这将为数据配置文件摘要统计信息提供一般模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
你还可以使用下面的示例来计算数据中的唯一行数和非重复行数。 可以使用名为 ValueDistAgg 的聚合转换将以下示例粘贴到数据流中。 此示例使用名为 "title" 的列。 请确保将 "title" 替换为你要用于获取值计数的数据中的字符串列。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>包含聚合中的所有列
这是一种通用聚合模式，演示如何在生成聚合时保留输出元数据中的其余列。 在这种情况下，我们使用 ```first()``` 函数来选择名称不是 "movie" 的每个列中的第一个值。 若要使用此函数，请创建名为 DistinctRows 的聚合转换，然后将其粘贴到脚本中现有 DistinctRows 聚合脚本的顶部。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>创建行哈希指纹 
使用数据流脚本中的此代码创建一个名为的新派生列 ```DWhash``` ，该派生列生成 ```sha1``` 三列的哈希。

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

你还可以使用以下脚本，使用流中存在的所有列生成行哈希，而无需命名每个列：

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg 等效项
此代码将充当 T-sql ```string_agg()``` 函数，并将字符串值聚合到一个数组中。 然后，可以将该数组强制转换为字符串，以便与 SQL 目标一起使用。

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>更新、upsert、插入、删除的次数
使用更改行转换时，可能需要计算从更改行策略生成的更新、upsert、插入、删除的数目。 在更改行后添加聚合转换，并将此数据流脚本粘贴到这些计数的聚合定义中。

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>使用所有列的非重复行
此代码段将向数据流中添加一个新的聚合转换，该转换将采用所有传入列，生成用于分组以消除重复项的哈希，然后提供每个重复项的第一个匹配项作为输出。 不需要显式命名列，它们会自动从传入数据流生成。

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>检查所有列中的 Null 值
这是一个代码段，你可以将其粘贴到数据流中，以一般检查所有列中的 NULL 值。 此方法利用架构偏移来浏览所有行中的所有列，并使用有条件拆分将行中的 Null 与无 Null 的行分隔开。 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

## <a name="next-steps"></a>后续步骤

从数据流[概述一文](concepts-data-flow-overview.md)开始探索数据流
