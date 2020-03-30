---
title: 映射数据流脚本
description: 数据工厂数据流脚本代码支持语言概述
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 92421125ecb5f4336922c6e6b4508fcdaf92be6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246392"
---
# <a name="data-flow-script-dfs"></a>数据流脚本 （DFS）

数据流脚本 （DFS） 是基础元数据，类似于编码语言，用于执行映射数据流中包含的转换。 每个转换都由一系列属性表示，这些属性提供了正确运行作业的必要信息。 通过单击浏览器 UI 顶部功能区上的"脚本"按钮，该脚本可以从 ADF 可见和编辑。

![脚本按钮](media/data-flow/scriptbutton.png "脚本按钮")

例如，`allowSchemaDrift: true,`在源转换中，告诉服务将来自源数据集的所有列包括在数据流中，即使它们未包含在架构投影中也是如此。

## <a name="use-cases"></a>用例
DFS 由用户界面自动生成。 您可以单击"脚本"按钮来查看和自定义脚本。 您还可以在 ADF UI 之外生成脚本，然后将脚本传递到 PowerShell cmdlet 中。 调试复杂数据流时，您可能会发现扫描脚本代码后面而不是扫描流的 UI 图形表示形式更容易。

下面是几个示例用例：
- 以编程方式生成许多相当相似的数据流，即"冲压"数据流。
- 在 UI 中难以管理或导致验证问题的复杂表达式。
- 调试并更好地了解执行期间返回的各种错误。

构建要与 PowerShell 或 API 一起使用的数据流脚本时，必须将格式化的文本折叠成一行。 您可以将选项卡和换行符保留为转义字符。 但是，文本的格式必须适合 JSON 属性。 脚本编辑器 UI 底部有一个按钮，该按钮将脚本格式化为单行。

![“复制”按钮](media/data-flow/copybutton.png "“复制”按钮")

## <a name="how-to-add-transforms"></a>如何添加转换
添加转换需要三个基本步骤：添加核心转换数据、重新路由输入流，然后重新路由输出流。 这在示例中是最容易的。
假设我们从一个简单的源开始，以像以下内容一样来存储数据流：

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

如果我们决定添加派生变换，首先我们需要创建核心转换文本，该文本具有一个简单的表达式来添加新大写列，称为`upperCaseTitle`：
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

然后，我们采用现有的 DFS 并添加转换：
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

现在，我们通过确定我们希望新转换之后的转换（在本例中`source1`）并将流的名称复制到新转换来重新路由传入流：
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

最后，我们确定要在此新转换之后进行转换，并将其输入流（本例中`sink1`）替换为新转换的输出流名称：
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
DFS 由一系列连接的转换组成，包括源、接收器和其他各种转换，这些转换可以添加新列、筛选数据、联接数据等。 通常，脚本以一个或多个源开头，然后是许多转换，最后以一个或多个接收器结束。

源都具有相同的基本构造：
```
source(
  source properties
) ~> source_name
```

例如，包含三列（电影 Id、标题、流派）的简单源是：
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

源以外的所有转换都具有相同的基本构造：
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

例如，采用列（标题）并用大写版本覆盖它的简单派生转换如下所示：
```
source1 derive(
  title = upper(title)
) ~> derive1
```

没有架构的接收器只是：
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>脚本代码段

### <a name="aggregated-summary-stats"></a>汇总汇总统计信息
向数据流添加名为"SummaryStats"的聚合转换，然后粘贴到下面的代码中，用于脚本中的聚合函数，替换现有的 SummaryStats。 这将为数据配置文件摘要统计信息提供通用模式。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
您还可以使用以下示例来计算数据中唯一行数和不同行数。 下面的示例可以粘贴到具有称为 ValueDistAgg 的聚合转换的数据流中。 此示例使用名为"标题"的列。 请确保将"标题"替换为要用于获取值计数的数据中的字符串列。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>将所有列包含在聚合中
这是一种通用聚合模式，演示如何在生成聚合时将剩余列保留在输出元数据中。 在这种情况下，我们使用 函数```first()```选择名称不是"movie"的每个列中的第一个值。 要使用此，请创建名为"明显行"的聚合转换，然后将该转换粘贴到现有"明显行"聚合脚本的顶部。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>创建行哈希指纹 
在数据流脚本中使用此代码创建一个称为生成```DWhash```三列```sha1```哈希的新派生列。

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

## <a name="next-steps"></a>后续步骤

从[数据流概述文章](concepts-data-flow-overview.md)开始探索数据流
