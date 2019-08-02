---
title: Azure 数据工厂映射数据流架构偏差
description: 使用架构偏差在 Azure 数据工厂中生成可复原的数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: b5777300f5033569caf3868218e747df3ff83a76
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640233"
---
# <a name="mapping-data-flow-schema-drift"></a>映射数据流架构偏移

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

“架构偏差”的概念是指源经常更改元数据。 可以动态添加、删除或更改字段、列和类型等。 如果不处理架构偏差，当上游数据源发生更改时，数据流就很容易发生更改。 当传入的列和字段发生更改时，典型的 ETL 模式将会失败，因为它们往往绑定到这些源名称。

为了防范架构偏差，必须在数据流工具中提供相应的机制，使数据工程师能够：

* 定义具有可变字段名称、数据类型、值和大小的源
* 定义可以使用数据模式而不是硬编码字段和值的转换参数
* 定义可以识别与传入字段相匹配而不是使用命名字段的表达式

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>如何在 ADF 中实现架构偏移映射数据流
ADF 以本机方式支持灵活的架构, 这些架构因执行而变化, 因此您无需重新编译数据流即可构建泛型数据转换逻辑。

* 在源转换中选择“允许架构偏差”

<img src="media/data-flow/schemadrift001.png" width="400">

* 选择此选项后，每次执行数据流都会从源读取所有传入字段，并通过整个流将其传递到接收器。

* 默认情况下, 所有新检测到的列 (偏移列) 将作为字符串数据类型到达。 如果希望自动从源中推断数据类型, 请在 "源转换" 中选择 "推断偏移列类型"。

* 请确保使用 "自动映射" 映射接收器转换中的所有新字段, 以便在目标中获取和着陆所有新字段, 同时在接收器上设置 "允许架构偏移"。

<img src="media/data-flow/automap.png" width="400">

* 在该方案中引入新字段时, 所有内容都将起作用, 并使用简单的源 > 接收器 (复制) 映射。

* 若要在处理架构偏差的该工作流中添加转换，可以使用模式匹配来按名称、类型和值对列进行匹配。

* 若要创建一个可识别“架构偏差”的转换，请在“派生列”或“聚合”转换中单击“添加列模式”。

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> 需要在数据流中做出体系结构方面的决策，使整个流接受架构偏差。 这样做可以防范源中发生架构更改。 但是，整个数据流中列和类型的前期绑定将会丢失。 Azure 数据工厂将架构偏差流视为后期绑定流，因此，当你生成转换时，整个流的架构视图中不会显示列名。

<img src="media/data-flow/taxidrift1.png" width="400">

在出租车演示示例数据流中，包含 TripFare 源的底部数据流中有一个示例架构偏差。 请注意，在“聚合”转换中，我们将对聚合字段使用“列模式”设计。 我们不会命名特定的列或按位置查找列，而是假设每次运行后数据可能会更改并且可能不按相同的顺序显示。

在此 Azure 数据工厂数据流架构偏差处理示例中，我们已生成一个聚合用于扫描“double”类型的列，并知道数据域包含每段行程的价格。 然后，可以针对源中的所有 double 字段执行聚合数学计算，不管该列在哪个位置载入，也不管该列的命名如何。

Azure 数据工厂数据流语法使用 $$ 来表示匹配模式中每个匹配的列。 也可以使用复杂字符串搜索和正则表达式函数来按列名进行匹配。 在这种情况下，我们将根据“double”类型的列的每个匹配项创建新的聚合字段名称，并将文本 ```_total``` 追加到每个匹配的名称： 

```concat($$, '_total')```

然后，我们将舍入和累加每个匹配列的值：

```round(sum ($$))```

使用 Azure 数据工厂数据流示例 "出租车演示" 时, 可以看到此架构偏移功能。 使用数据流设计图面顶部的“调试”切换开关打开调试会话，以交互方式查看结果：

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>访问下游新列
使用列模式生成新列时, 可以在数据流转换后使用以下方法来访问这些新列:

* 使用 "byPosition" 按位置编号标识新列。
* 使用 "byName" 按其名称标识新列。
* 在列模式下, 使用 "名称"、"流"、"位置" 或 "类型" 或它们的任意组合来匹配新列。

## <a name="rule-based-mapping"></a>基于规则的映射
选择和接收器转换支持通过基于规则的映射的模式匹配。 这将允许你构建可将偏移列映射到列别名并将这些列接收到目标的规则。

## <a name="next-steps"></a>后续步骤
在[数据流表达式语言](data-flow-expression-functions.md)中, 你将找到用于列模式和架构偏移的其他功能, 包括 "byName" 和 "byPosition"。
