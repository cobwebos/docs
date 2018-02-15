---
title: "在 Azure 机器学习中使用数据转换来准备数据 | Microsoft Docs"
description: "本文提供的完整转换列表可用于在 Azure 机器学习中准备数据。"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: d91985849bd2483e8a28a01d63d253960190829d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>在 Azure 机器学习中使用数据转换来准备数据

Azure 机器学习中的转换使用给定格式的数据、对数据执行操作（例如更改数据类型），然后以新格式生成数据。 每个转换有其自身的接口和行为。 通过数据流中的步骤将多个转换链接在一起后，可对数据执行复杂且可重复的转换。 这是数据准备功能的核心所在。

下面是可在 Azure 机器学习中使用的转换。 

## <a name="column-selection"></a>列选择 
此列表中的许多转换均可用于单列或多列。 要选择多个列，请使用 Ctrl 键。 要选择一系列的列，请使用 Shift 键。

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>主菜单或网格标头中的转换 
可通过主菜单上的“转换”选项访问转换。 也可通过右键单击数据网格中的列名称选择转换。 如果选择了多个列，则右键单击任一列都会显示转换菜单。

右键单击菜单中只会显示对所选数据类型有效的转换。 主菜单提供所有转换，但禁用了与所选列不相关的转换。

右键单击某个单元格会显示上下文转换的一小部分。 这些转换为“复制”、“替换”和“筛选”。 这些转换可以感知数据类型，因此数值列的选项不同于字符串列。

## <a name="derive-column-by-example"></a>按示例派生列
使用此转换可将新列创建为一个或多个现有列的派生列。 转换会查看（所选）输入列和给定的示例，然后确定新列中所需的输出。 

若要使用此转换，请选择一个或多个列。 按示例添加一个新的（空白）派生列。 在派生列中键入想要看到的示例（假设它派生自其他列），“按示例”技术即会尝试填充列中的所有其他单元格。 

对于复杂的示例，可能需要提供多个示例。 为此，请选择另一个单元格并键入另一个示例。

“按示例”技术使用所选列来尝试确定示例的含义。 调用此转换时，如果没有选择任何列，则使用当前行的所有单元格。 仅选择需要的列可以生成更准确的结果。

可以在调用转换前选择列。 打开转换编辑器后，每个列顶部的复选框会指示已选择哪些列作为输入。 可以使用列标题中的复选框来添加或删除可选择的列。

有关“按示例派生列”转换的详细说明以及更多示例，请参阅[按示例派生列参考](data-prep-derive-column-by-example.md)。  

## <a name="split-column-by-example"></a>按示例拆分列
此转换选取现有列并使用“按示例”引擎尝试将该列拆分为 n 个其他列。 可在后续生成的列上运行自动拆分。

有关“按示例拆分列”转换的详细说明以及更多示例，请参阅[按示例拆分列参考](data-prep-split-column-by-example.md)。

## <a name="expand-json"></a>展开 JSON

使用此转换可通过扩展包含有效 JSON 文本的列来添加多个列。

有关“扩展 JSON”转换的详细说明以及更多示例，请参阅[扩展 JSON 参考](data-prep-expand-json.md)。


## <a name="combine-columns-by-example"></a>按示例合并列

使用此转换可通过组合多个列的值来添加一个新列。 

有关“按示例组合列”转换的详细说明以及更多示例，请参阅[按示例组合列参考](data-prep-combine-columns-by-example.md)。


## <a name="duplicate-column"></a>复制列
此转换创建一个或多个选定列的确切副本，并使用派生自原始列名称的新名称为其命名。

## <a name="text-clustering"></a>文本群集 
此转换旨在使用应为相同值的不一致值，并将其组合到一起。  

借助此转换可分析单列中的值的相似性并将其组合成群集。 对于每个群集，都有一个规范值，该值用于替换群集中所有的实例和实例值。 可删除整个群集，还可编辑规范值。 可以从给定群集中删除实例。 可更改用于将实例组合成群集的相似性分数阈值筛选器。

默认情况下，此转换将所有群集实例值替换为该群集的规范值，从而创建包含新值的新列。 还可将每个实例的相似性分数添加到新列（可对其命名）以便稍后在数据流中使用。

## <a name="replace-values"></a>替换值
使用此转换将一个字符串替换为另一字符串。 源字符串可以是部分字符串或完整单元格，可替换单列或多列。 搜索字符串支持搜索特殊字符和常规字符。 

## <a name="replace-na-values"></a>替换 NA 值
使用此转换可将各种不同形式的 NA（N/A、NA、null、NaN 等）或空字符串替换为单个值，使其保持一致。 此转换支持单列或多列，且只有在选中列后才会列出。 未选中任何列时，此转换不会出现在主转换菜单上。

## <a name="replace-missing-values"></a>替换缺失值
此转换将缺失数据替换为单个值，支持单列或多列。 只有在选中列后，才会列出此转换。 未选中任何列时，此转换不会出现在主转换菜单上。

## <a name="replace-error-values"></a>替换错误值
此转换将错误替换为单个值，支持对单列或多列进行替换。 只有在选中列后，才会列出此转换。 未选中任何列时，此转换不会出现在主转换菜单上。

## <a name="trim-string"></a>裁剪字符串
此转换从单列或多列中删除前导和尾部“空白”字符（包括空格、制表符等）。

## <a name="adjust-precision"></a>调整精度
此转换可为数值列设置小数位数。

## <a name="rename-column"></a>重命名列
此转换更改选定列的名称。 还可通过单击列名称，在列标题中以内联方式调用此转换。

## <a name="remove-column"></a>删除列
此转换可删除所选列，适用于单列或多列。 

## <a name="keep-column"></a>保留列
此转换可仅保留所选列，适用于单列或多列。

## <a name="handle-path-column"></a>图柄路径列
在导入文件期间，“添加数据源”功能会自动向数据集添加路径列。 它包含形成数据集路径的完全限定文件名。 此转换从数据集添加列或删除多余的列。

## <a name="convert-field-type-to-numeric"></a>将字段类型转换为数字
此转换将列类型更改为数字。 可指定非整数数据的分隔符。 默认情况下，此转换不会提示输入分隔符，因此可使用“编辑”菜单项调用编辑器。 此转换适用于单列或多列。

## <a name="convert-field-type-to-date"></a>将字段类型转换为日期
此转换将列类型更改为日期。 使用的是默认日期/时间格式，但可使用 `strftime` 指令对其重写。 还可在前面附加包含固定日期的时间值。

默认情况下，此转换不会提示输入格式，因此可在最终步骤中使用“编辑”菜单项调用编辑器。 此转换适用于单列或多列。

仅能将介于 9-22-1677 和 4-11-2262 之间的日期转换为日期类型。

## <a name="convert-field-type-to-boolean"></a>将字段类型转换为布尔值
此转换将列类型更改为 true/false。 转换支持将多个值映射为 true 或 false，且可编辑这些映射。 它还支持将 true/false 映射表中不存在的值映射到一个常量。 此转换适用于单列或多列。

## <a name="convert-field-type-to-string"></a>将字段类型转换为字符串
此转换将列类型更改为字符串，适用于单列或多列。

## <a name="convert-unix-timestamp-to-datetime"></a>将 Unix 时间戳转换为日期时间
此转换可识别 Unix 时间戳格式，即使它在数据中表示为字符串。 它可在数据准备过程中将时间戳正确地转换为日期类型。

## <a name="filter"></a>筛选器
此转换支持基于一个或多个列中的值筛选行。 筛选条件依赖于每个列的数据类型，因此，可按“包含”或“不包含”条件来筛选字符串列。 可按“大于”或“小于”条件来筛选数字列。

如果从主菜单或通过右键单击列标题来调用“筛选”转换，则可将失败行分支到其他数据流。 主数据流继续在筛选**入**行中执行，并创建包含筛选**出**的所有行的新数据流。

## <a name="use-first-row-as-headers"></a>将第一行用作标头
此转换将数据集中的第一行提升为列名称。 如果某些列的第一行中不包含数据，则系统会自动生成名称。 使用此转换意味着最早从行 2 开始导入数据。 根据“跳过行”设置，甚至可以在数据集中更往下的行开始导入。 还可使用“跳过行”来删除提升，并仅使用自动生成的名称。

## <a name="join"></a>Join
此转换用于将两个数据流联接起来。 可选择将哪个联接输出作为结果：联接中的成功行、联接中的“左”失败行或联接中的“右”失败行。

“联接”转换从单个数据流开始，并将该数据流选作联接的一侧。 然后，它会提示选择另一数据流作为联接的另一侧。 选择这两个数据流后，转换要求选择联接每侧的单列以进行联接。 如果联接需要多列，则在启动转换前创建一个派生列，以创建仅供联接使用的新串连列。 该转换尝试建议联接键，如果可能，将自动生成派生列。

完成联接后，会显示联接的 Sankey 图示视图。 彼此相关的线条宽度表示通过联接流的该部分移动的行数。 通过右侧面板，可单独选择成功的行、左侧失败行或右侧失败行。 还可选择单个分支。

## <a name="append-rows"></a>追加行
此转换将另一个数据流中的数据追加到当前数据流。 它会按位置映射列，以便在末尾添加新行。

## <a name="append-columns"></a>追加列
此转换将另一个数据流中的新列追加到当前数据流。 它将新列添加到右侧。 但不会按列排列数据。

## <a name="summarize"></a>总结
此转换计算一个或多个选定列中唯一值组合的聚合。 

支持的聚合为：COUNT、SUM、MIN、MAX、MEAN、VARIANCE 和 STANDARD DEVIATION。 给定列的聚合列表经过筛选，只返回适用于该数据类型的聚合。 不适用的聚合被禁用。 例如，无法计算字符串列的平均值，但可以计算最小值和最大值。

有可用编辑器后，从列标头向上拖动到左上方的面板中，要聚合的列会在此处显示。 此面板是分层的，因此可以执行嵌套聚合。 右上方的编辑器面板用于选择应用于列的聚合。 一个列可聚合一次或多次。 选择至少一个聚合后，右下方的网格在其聚合窗体中预览数据。 

此转换类似于 `ANSI-SQL GROUP BY`。

## <a name="remove-duplicates"></a>删除重复项
此转换可删除选定的一列或多列中存在重复值的整行。 如果未选择任何列，则只会删除所有列值都相同的行。

## <a name="sort"></a>排序
此转换对数据排序。 可按单列或多列排序，每列可按升序（默认）或降序（可在编辑器中更改）排序。

此转换类似于 `ANSI-SQL ORDER BY`。

## <a name="output-transforms"></a>输出转换
以下转换会输出数据。 可在单个流中包含多个写入块，以便在不同时间点写出数据。

### <a name="write-to-csv"></a>写入到 CSV
此转换从数据流中的当前点以 CSV 格式写出数据。 它可控制位置（本地或远程）以及关于文件的各种设置。

### <a name="write-to-parquet"></a>写入到 Parquet
此转换从数据流中的当前点以 Parquet 格式写出数据。 它可控制位置（本地或远程）以及关于文件的各种设置。

## <a name="script-based-transforms"></a>基于脚本的转换
以下转换使用脚本 (Python) 执行核心产品中缺少的功能。 

>[!NOTE]
>使用其中任一转换前，请先阅读[使用 Python 扩展性](data-prep-python-extensibility-overview.md)。

### <a name="add-column-script"></a>添加列（脚本）
通过此转换可使用 Python 表达式将列添加到数据。
有关详细信息，请参阅[用于派生新列的示例 Python 代码](data-prep-appendix10-sample-custom-column-transforms-python.md)。

### <a name="advanced-filter-script"></a>高级筛选器（脚本）
使用此转换来编写 Python 行级筛选器。
有关详细信息，请参阅[示例筛选表达式](data-prep-appendix6-sample-filter-expressions-python.md)。

### <a name="transform-dataflow-script"></a>转换数据流（脚本）
此转换可将 Python 应用到整个数据集。
有关详细信息，请参阅[转换数据流转换示例](data-prep-appendix7-sample-transform-data-flow-python.md)。

此转换还可将 Python 应用到整个数据分区。
有关详细信息，请参阅[转换数据流转换示例](data-prep-appendix7-sample-transform-data-flow-python.md)。

### <a name="write-dataflow-script"></a>编写数据流（脚本）
此转换使用 Python 来编写整个数据集。
有关详细信息，请参阅[用于派生新列的示例 Python](data-prep-appendix9-sample-destination-connections-python.md)。



