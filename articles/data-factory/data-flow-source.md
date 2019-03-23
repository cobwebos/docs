---
title: Azure 数据工厂映射数据流源转换
description: Azure 数据工厂映射数据流源转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 54302f97913fd01dc8f8e4a8d987a407c8bdf9a7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369159"
---
# <a name="mapping-data-flow-source-transformation"></a>映射数据流源转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

源转换配置用于将数据导入数据流的数据源。 可以在单个数据流中使用多个源转换。 始终开始设计你使用源转换数据流动。

> [!NOTE]
> 每个数据流至少需要一个源转换。 可添加其他任意数目的源以完成数据转换。 可以使用联接或联合转换将这些源联接到一起。 当在调试会话中调试数据的流时，将从使用采样设置或调试源限制的源读取数据。 但是，任何数据将不写入到接收器直到管道数据流活动中执行数据的流。 

![源转换选项](media/data-flow/source.png "源")

每个数据流源转换必须与一个数据工厂数据集相关联。 数据集定义的形状和位置数据以写入或读取。 可以在您的源中使用通配符和文件列表来一次处理多个文件时使用文件源。

## <a name="data-flow-staging-areas"></a>数据流暂存区域

数据流使用“暂存”数据集，这些数据集全部位于 Azure 中。 这些数据流数据集用于数据暂存，以执行数据转换。 数据工厂可以访问将近 80 个不同的本机连接器。 若要将其他这些源中的数据包含到你自己的数据流中，请先使用复制活动将该数据暂存到其中一个数据流数据集暂存区域。

## <a name="options"></a>选项

### <a name="allow-schema-drift"></a>允许架构偏差
如果源列经常更改，请选择“允许架构偏差”。 此设置可让你的源传入的字段通过转换流向接收器

### <a name="validate-schema"></a>验证架构

![公共源](media/data-flow/source1.png "公共源 1")

如果源数据的传入版本与定义的架构不匹配，则数据流的执行将会失败。

### <a name="sampling"></a>采样
使用采样来限制源中的行数。  这是测试或从您出于调试目的的源进行数据采样时很有用。

## <a name="define-schema"></a>定义架构

![源转换](media/data-flow/source2.png "源 2")

对于不是强类型化的源文件类型（即平面文件而不是 Parquet 文件），应在源转换中为此处的每个字段定义数据类型。 然后，可以在“选择”转换中更改列名，在“派生列”转换中更改数据类型。 

![源转换](media/data-flow/source003.png "数据类型")

对于强类型化的源，可以修改在后续选择转换中的数据类型。 

### <a name="optimize"></a>优化

![源分区](media/data-flow/sourcepart.png "分区")

在源转换的“优化”选项卡上，可以看到名为“源”的附加分区类型。 仅当已选择“Azure SQL DB”作为源时，才会显示此类型。 这是因为 ADF 想要并行化连接，以针对 Azure SQL 数据库源执行大型查询。

将 SQL 数据库源中的数据分区是可选操作，但此操作对于大型查询很有用。 可以使用两个选项：

### <a name="column"></a>列

从源表中选择一个用作分区依据的列。 此外，必须设置最大连接数。

### <a name="query-condition"></a>查询条件

可视需要选择基于查询将连接分区。 使用此选项时，只需在 WHERE 谓词中输入内容即可。 例如 year > 1980

## <a name="source-file-management"></a>源文件管理
![新源设置](media/data-flow/source2.png "新设置")

* 通配符路径选取一系列从你的源文件夹与模式匹配的文件。 这将覆盖您的数据集定义中设置了任何文件。
* 文件列表。 与文件集相同。 指向你创建的、包含要处理的相对路径文件列表的文本文件。
* 用于存储文件名的列将在数据列中存储来自源的文件名。 请在此处输入新名称以存储文件名字符串。
* 完成后（数据流执行后你可以选择不对源文件执行任何操作、删除源文件或移动源文件）。 移动路径是相对路径。

### <a name="sql-datasets"></a>SQL 数据集

使用 Azure SQL 数据库或 Azure SQL 数据仓库作为源时，可以使用其他选项。

* 查询：输入针对源的 SQL 查询。 设置查询会替代在数据集中选择的任何表。 请注意，此处不支持 Order By 子句。 但是，您可以此处设置一个完整的 SELECT FROM 语句。

* 批大小：输入批大小，以将大型数据分块成批大小的读取。

> [!NOTE]
> 仅当使用管道中的“执行数据流”活动通过管道运行（管道调试或执行运行）执行数据流时，文件操作设置才会执行。 文件操作不会在数据流调试模式下执行。

### <a name="projection"></a>投影

![投影](media/data-flow/source3.png "投影")

类似于数据集中的架构，源中的投影定义源数据中的数据列、数据类型和数据格式。 如果文本文件中未定义架构，请单击“检测数据类型”，以要求 ADF 尝试采样和推断数据类型。 可以使用“定义默认格式”按钮设置自动检测的默认数据格式。 可以在后续的“派生列”转换中修改列数据类型。 可以使用“选择”转换修改列名。

![默认格式](media/data-flow/source2.png "默认格式")

## <a name="next-steps"></a>后续步骤

开始使用[派生列](data-flow-derived-column.md)和[选择](data-flow-select.md)生成数据转换。
