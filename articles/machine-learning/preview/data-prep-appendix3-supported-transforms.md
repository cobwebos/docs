---
title: "适用于 Azure 机器学习数据准备的受支持转换 | Microsoft Docs"
description: "本文档提供了适用于 Azure ML 数据准备的转换的完整列表"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fd3f8157e27847b99e59465063111a6d6c8c713d
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="supported-transforms-for-this-release"></a>此版本支持的转换
下面的列表列出和总结了可用于此版本的转换。 

此版本支持的转换如下所述。

## <a name="column-selection"></a>列选择 
下面列出的许多转换都适用于一个或多个列。 若要选择多个列，按住 Control 并单击每列，或按住 Shift 并单击一个列范围。

## <a name="transforms-from-main-menu-andor-grid-header"></a>从主菜单和/或网格标头转换 
可以从主菜单上的转换选项访问转换。 也可以通过右键单击数据网格中的列名称选择转换。 如果选择了多个列，则右键单击任一列都会显示转换菜单。

右键单击菜单上仅显示所选数据类型的有效转换，主菜单显示所有转换，但会灰显与所选列不相关的转换。

右键单击单元格将显示上下文转换的一小部分。 这些转换是复制、替换和筛选器，因为这些转换可以感知数据类型，所以数值列的选项不同于字符串列。

## <a name="derive-column-by-example"></a>按示例派生列
此转换允许将新列创建为一个或多个现有列的派生列。 通过查看输入（所选）列和给定的示例执行此操作，以确定新列中所需的输出。 

若要使用它，请选择一个或多个列。 按示例添加一个新（空）的派生列。 在派生列中键入想要看到的示例（假设它派生自其他列），“按示例”技术尝试填充列中的所有其他单元格。 

对于复杂的示例，有必要提供多个示例，选择其他单元格并键入另一个示例。

“按示例”技术使用所选列来尝试确定示例的含义。 调用此转换时，如果没有选择任何列，则使用当前行的所有单元格。 仅选择需要的列可以生成更准确的结果。

可以在调用转换前选择列。 启动转换编辑器后，将可以通过每列顶部的复选框查看哪些列选作输入。 可以使用列标头中的复选框来添加和删除选择的列。

有关**按示例派生列**转换的详细说明以及更多示例，请参阅：[按示例派生列参考](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>按示例拆分列
选取现有列并使用“按示例”引擎尝试将该列拆分为 n 个其他列。 可以在后续生成的列上运行自动拆分。

有关**按示例拆分列**转换的详细说明以及更多示例，请参阅：[按示例拆分列参考](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>展开 JSON

**展开 JSON** 转换使用户能够通过展开包含有效 JSON 文本的列来添加多个列。

有关**展开 JSON** 转换的详细说明以及更多示例，请参阅：[展开 JSON 参考](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>按示例合并列

允许用户通过组合多个列的值来添加一个新列。 

有关**按示例组合列**转换的详细说明以及更多示例，请参阅：[按示例组合列参考](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>复制列
此转换创建列的确切副本，并用派生自原始列名称的新名称为其命名。 此转换支持一个或多个列。

## <a name="text-clustering"></a>文本群集 
此转换旨在使用应为相同值的不一致值，并将其组合到一起。  

运行此转换时，对单列中的值进行分析以查找相似性。 然后将其组合到群集中。 对于每个群集，都有一个规范值（该值是替换群集中所有实例的值）和实例值。 可以删除完整的群集，可以编辑规范值。 可以从给定群集中删除实例。 此外，可以更改用于将实例组合到群集的相似性分数阈值筛选器。

默认情况下，此转换将所有群集实例值替换为该群集的规范值，从而创建包含新值的新列。 也可以将每个实例的相似性分数添加到新列（可对其命名）以便稍后在数据流中使用。

## <a name="replace-values"></a>替换值
此转换允许一个字符串被另一个字符串替换，源字符串可以是部分字符串或完整的单元格。 替换可以应用到单列或多列。 搜索字符串支持搜索特殊字符和常规字符。 

## <a name="replace-na-values"></a>替换 NA 值
允许将各种不同形式的 NA（N/A、NA、NULL、NaN 等）或空字符串替换为单个值，以使其保持一致。 此转换支持一个或多个列。 仅当选中某列时才列出此转换，未选中任何列时将不会在主转换菜单上显示。

## <a name="replace-missing-values"></a>替换缺失值
执行将缺失数据替换为单个值。 此转换支持一个或多个列。 仅当选中某列时才列出此转换，未选中任何列时将不会在主转换菜单上显示。

## <a name="replace-error-values"></a>替换错误值
执行将错误替换为单个值。 此转换支持一个或多个列。 仅当选中某列时才列出此转换，未选中任何列时将不会在主转换菜单上显示。

## <a name="trim-string"></a>裁剪字符串
从单列或多列删除前导和尾部“空格”字符（包括空格、选项卡等）。

## <a name="adjust-precision"></a>调整精度
允许为数值列设置小数位的位数

## <a name="rename-column"></a>重命名列
更改列名称，也可以通过单击列名称在列标头中以内联方式调用此转换。

## <a name="remove-column"></a>删除列
删除所选列，此转换适用于单列或多列。 

## <a name="keep-column"></a>保留列
仅保留所选列，此转换适用于单列或多列。

## <a name="handle-path-column"></a>图柄路径列
导入文件时，会通过向导自动向数据集添加路径列。 它包含形成数据集路径的完全限定文件名。 此转换从数据集添加列或删除多余的列。

## <a name="convert-field-type-to-numeric"></a>将字段类型转换为数字
将列类型转换为数字。 如果没有非整型数据，则指定分隔符。 默认情况下，此转换不会提示输入分隔符，使用“编辑”菜单项调用编辑器。 此转换适用于单列或多列。

## <a name="convert-field-type-to-date"></a>将字段类型转换为日期
将列类型更改为日期。 使用默认日期/时间格式，但可以使用 `strftime` 指令对其重写。 也可以在前面附加包含固定日期的时间值。

默认情况下，此转换不会提示输入格式，使用结果步骤上的“编辑”菜单项调用编辑器。 此转换适用于单列或多列。

仅能将介于 9-22-1677 和 4-11-2262 之间的日期转换为日期类型。

## <a name="convert-field-type-to-boolean"></a>将字段类型转换为布尔值
将列类型更改为 true/false。 此转换支持将多个值映射为 true 或 false，可以编辑这些映射。 它还支持将值映射到一个 true/false 映射表中不存在的常量。 此转换适用于单列或多列。

## <a name="convert-field-type-to-string"></a>将字段类型转换为字符串
将列类型更改为字符串。 此转换适用于单列或多列。

## <a name="convert-unix-timestamp-to-datetime"></a>将 Unix 时间戳转换为日期时间
此转换理解 Unix 时间戳格式（即使它在数据中表示为字符串）并在数据准备过程中将其正确地转换为日期类型。

## <a name="filter"></a>筛选器
筛选器转换支持基于一列或多列中的值对行进行筛选，筛选器的条件取决于每列的数据类型，因此可以按“包含”或“不包含”来筛选字符串列，但不能对数值列执行此操作。 而“大于”“小于”条件适用于数值列，不适用于字符串列。

对于从主菜单或通过右键单击列标头来调用筛选器转换的情况，可以将失败行分支到其他数据流。 因此，主数据流继续在筛选**入**行中执行，并创建了包含筛选**出**的所有行的新数据流。

## <a name="use-first-row-as-headers"></a>将第一行用作标头
此转换将数据集的第一行提升为列名称，如果某些列在第一行没有数据，则会自动生成名称。 使用此转换意味着最早从行 2 开始导入数据。 根据跳过行设置，导入启动甚至可以在数据集中进一步细化。 它还可以用于删除提升并仅使用自动生成的名称。

## <a name="join"></a>Join
此转换用于将两个数据流联接起来。 它允许选择将哪个联接输出作为结果、联接中的成功行、联接中的“左”失败行或联接中的“右”失败行。

从单个数据流启动联接向导，并选择该数据流作为联接的一侧，然后提示用户为联接的另一侧选择其他数据流。 选择这两个数据流后，向导要求选择接点每侧的单列以进行联接。 如果联接需要多列，则在启动向导前创建派生列，以创建仅供联接使用的新（串连）列。 该向导尝试建议联接键，如果可能，将自动生成派生列。

联接完成后，将显示联接的 Sankey 关系图视图，相对于彼此的行宽表示在联接流部分移动的行数。 右侧面板允许以独占方式选择成功行、左失败行、右失败行或仅选择一个分支。

## <a name="append-rows"></a>追加行
此转换将其他数据流中的数据追加到当前流，它按位置映射列，以在末尾添加新行。

## <a name="append-columns"></a>追加列
此转换将其他数据流中的新列追加到当前流，它将新列添加到右侧，它不会尝试在行中排列数据。

## <a name="summarize"></a>总结
为唯一值的组合选择列并计算聚合。

支持的聚合：COUNT、SUM、MIN、MAX、MEAN、VARIANCE、STANDARD DEVIATION。 对给定列的聚合列表进行了筛选，只筛选出适用于该数据类型的聚合。 不适用的聚合显示为灰色。作为示例，无法计算字符串列的平均值，但可以计算最小值和最大值。

类似于 ANSI-SQL GROUP BY

有可用的编辑器后，从列标头向上拖动到左上角的面板中，要聚合的列会在此处显示。 此面板是分层的，因此可以执行嵌套聚合。 右上角的编辑器面板用于选择应用于列的聚合。 一个列可聚合一次或多次。 选择至少一个聚合后，右下方的网格在其聚合窗体中预览数据。 

## <a name="remove-duplicates"></a>删除重复项
此转换删除存在重复值的整行。 重复值由调用转换或使用编辑器选择的一个或多个列定义。 如果未选择任何列，则删除的行正是所有列值都相同的行。

## <a name="sort"></a>排序
此转换对数据进行排序，可通过一列或多列执行此操作，每列都可以按升序（默认）或降序（可在编辑器中更改）排序。

类似于 ANSI-SQL ORDER BY

## <a name="output-transforms"></a>输出转换
以下转换输出数据。 可在单个流中具有多个写入块，以在不同点写出数据。

### <a name="write-to-csv"></a>写入到 CSV
此转换从数据流中的当前点以 CSV 格式写出数据。  它允许控制位置（本地或远程）以及围绕文件的各种设置。

### <a name="write-to-parquet"></a>写入到 Parquet
此转换从数据流中的当前点以 Parquet 格式写出数据。 它允许控制位置（本地或远程）以及围绕文件的各种设置。

## <a name="script-based-transforms"></a>基于脚本的转换
以下转换全部使用脚本 (Python) 执行核心产品中缺少的功能。 
[使用其中任一转换前，请阅读此处文档了解扩展性](data-prep-python-extensibility-overview.md)：

### <a name="add-column-script"></a>添加列（脚本）
允许使用 Python 表达式将列添加到数据[参阅附录 10 以了解详细信息](data-prep-appendix10-sample-custom-column-transforms-python.md)

### <a name="advanced-filter-script"></a>高级筛选器（脚本）
允许写入 Python 行级别筛选器[参阅附录 6 了解详细信息](data-prep-appendix6-sample-filter-expressions-python.md)

### <a name="transform-dataflow-script"></a>转换数据流（脚本）
允许将 Python 应用到整个数据集

[参阅附录 7 了解详细信息](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="transform-dataflow-script"></a>转换数据流（脚本）
允许将 Python 应用到整个数据分区

[参阅附录 7 了解详细信息](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="write-dataflow-script"></a>编写数据流（脚本）
允许将 Phthon 应用到写出和整个数据集[参阅附录 10 了解详细信息](data-prep-appendix9-sample-destination-connections-python.md)




