---
title: 设置 Azure 数据工厂中映射 Data Flow 功能的源转换
description: 了解如何设置映射数据流中的源转换。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 5b53819c1d30f6cd62c5941d4b44d70a4996daad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67117882"
---
# <a name="source-transformation-for-mapping-data-flow"></a>源映射数据流转换 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

源转换将配置数据源的数据流。 数据流可以包含多个源转换。 在设计数据流动时始终以与源转换。

每个数据流需要至少一个源转换。 根据需要来完成数据转换添加任意多个源。 可以加入这些源和联接转换或联合的转换。

> [!NOTE]
> 调试时数据的流，是从源中使用的采样设置或调试源限制读取数据。 若要将数据写入到接收器，必须从数据流活动的管道运行数据的流。 

![源在源设置选项卡上的转换选项](media/data-flow/source.png "源")

将在数据流源转换与一个数据工厂数据集相关联。 数据集定义的形状和你想要从读取或写入的数据的位置。 可以在您的源中使用通配符和文件列表来一次处理多个文件。

## <a name="data-flow-staging-areas"></a>数据流量分段区域

适用于数据流*暂存*所有在 Azure 中的数据集。 用于暂存正在转换你的数据时使用这些数据集。 

数据工厂可以访问几乎 80 本机连接器。 若要在数据流中包含这些其他来源的数据，请使用复制活动工具来暂存数据的流数据集过渡区域之一中该数据。

## <a name="options"></a>选项

选择你的数据的架构和采样选项。

### <a name="allow-schema-drift"></a>允许架构偏差
选择**允许架构偏差**如果源列会经常发生变化。 此设置允许所有传入的源字段流经到接收器的转换。

### <a name="validate-schema"></a>验证架构

如果源数据的传入版本不匹配定义的架构，将无法运行流的数据。

![公共源设置，显示的选项用于验证架构、 允许架构偏差和采样](media/data-flow/source1.png "公共源 1")

### <a name="sample-the-data"></a>对数据进行采样
启用**采样**来限制您的源中的行数。 测试或从您的源用于调试目的的示例数据时，请使用此设置。

## <a name="define-schema"></a>定义架构

当源文件不强类型 （适用于示例中，平面文件而不是 Parquet 文件），在源转换中定义下面的每个字段的数据类型。  

![源的定义架构选项卡上的转换设置](media/data-flow/source2.png "源 2")

更高版本可以更改列名称选择转换中。 使用派生列转换将更改的数据类型。 对于强类型化的源，可以修改选择转换更高版本中的数据类型。 

![选择转换中的数据类型](media/data-flow/source003.png "数据类型")

### <a name="optimize-the-source-transformation"></a>优化源转换

上**优化**选项卡上的源转换中，你可能会看到**源**分区类型。 仅当您的源是 Azure SQL 数据库时，此选项才可用。 这是因为数据工厂会尝试并行对您的 SQL 数据库源运行大型查询来建立连接。

![源分区设置](media/data-flow/sourcepart3.png "分区")

你没有对数据进行分区上您的 SQL 数据库源，但分区可用于大型查询。 可以一列或查询基于您的分区。

### <a name="use-a-column-to-partition-data"></a>使用对数据进行分区的列

从源表中，选择上到分区列。 此外设置分区数。

### <a name="use-a-query-to-partition-data"></a>使用对数据进行分区的查询

您可以选择分区基于查询的连接。 只需输入 WHERE 谓词的内容。 例如，输入 > 1980 年。

## <a name="source-file-management"></a>源文件管理

选择要管理您的源中的文件的设置。 

![新的源设置](media/data-flow/source2.png "新设置")

* **通配符路径**:从源文件夹，选择一系列与模式匹配的文件。 此设置将覆盖您的数据集定义中的任何文件。

通配符的示例：

* ```*``` 表示字符的任何组
* ```**``` 表示递归目录嵌套
* ```?``` 替换一个字符
* ```[]``` 与方括号中的多个字符之一相匹配

* ```/data/sales/**/*.csv``` 获取 /data/sales 下的所有 csv 文件
* ```/data/sales/20??/**``` 在 20 世纪中获取所有文件
* ```/data/sales/2004/*/12/[XY]1?.csv``` 在 2004 年 12 月开始 X 中获取所有 csv 文件或 Y 为前缀的一个 2 位数字

容器必须在数据集中指定。 通配符路径因此还必须包括您从根文件夹的文件夹路径。

* **文件的列表**:这是文件组。 创建包含要处理的相对路径文件列表的文本文件。 指向此文本文件。
* **用于存储文件的名称列**:在你的数据中的列中存储源文件的名称。 请在此处输入新名称以存储文件名字符串。
* **完成后**:选择不执行任何操作与源文件后数据流运行、 删除源文件，或移动的源文件。 要移动的路径是相对的。

若要将源文件移动到另一个位置后处理，首先选择"移动"文件操作。 然后，设置"from"目录。 如果您不将任何通配符来用于你的路径，则"发件人"设置将与源文件夹相同的文件夹。

如果必须将通配符源路径，例如：

```/data/sales/20??/**/*.csv```

您可以指定为"from"

```/data/sales```

和"to"作为

```/backup/priorSales```

在这种情况下，相对于 /backup/priorSales 移动已指明其出处的 /data/sales 下的所有子目录。

### <a name="sql-datasets"></a>SQL 数据集

如果您的源是 SQL 数据库或 SQL 数据仓库中，则必须的源文件管理的其他选项。

* **查询**：输入针对源的 SQL 查询。 此设置将覆盖你在数据集中选择任何表。 请注意， **Order By**子句不受支持，但可以设置一个完整的 SELECT FROM 语句。 此外可以使用用户定义表函数。 **选择 * 从 udfGetData()** 是返回一个表的 SQL 中的 UDF。 此查询将生成可以使用在数据流中的源表。
* **批大小**：输入要分块读取到大型数据的批处理大小。

> [!NOTE]
> 文件操作仅当运行在管道中使用执行数据流活动的管道运行 （管道调试或执行运行） 从启动流的数据。 文件操作*不这样做*数据流调试模式运行。

### <a name="projection"></a>投影

在数据集中的架构，如源中的数据投影将定义的数据列、 类型和源数据中的格式。 

![投影选项卡上的设置](media/data-flow/source3.png "投影")

如果您的文本文件有没有定义的架构，选择**检测数据类型**，以便数据工厂将示例和推断数据类型。 选择**定义默认格式**自动检测到的默认数据格式。 

可以修改在更高版本派生列转换中的列数据类型。 使用选择的转换来修改列名称。

![有关默认数据格式设置](media/data-flow/source2.png "默认格式")

## <a name="next-steps"></a>后续步骤

开始构建[派生列转换](data-flow-derived-column.md)和一个[选择转换](data-flow-select.md)。
