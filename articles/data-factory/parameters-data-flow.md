---
title: 将映射数据流参数化
description: 了解如何从数据工厂管道中参数化映射数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780363"
---
# <a name="parameterizing-mapping-data-flows"></a>将映射数据流参数化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

在 Azure 数据工厂中映射数据流支持使用参数。 定义数据流定义内的参数，并在整个表达式中使用它们。 参数值由调用管道通过执行数据流活动设置。 有三个选项可用于设置数据流活动表达式中的值：

* 使用管道控制流表达式语言设置动态值
* 使用数据流表达式语言设置动态值
* 使用任意一种表达式语言设置静态文本值

使用此功能可以使您的数据流过常规用途、灵活和可重用性。 可以用这些参数参数化数据流设置和表达式。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在映射数据流中创建参数

若要将参数添加到数据流，请单击 "数据流" 画布的空白部分以查看 "常规" 属性。 在 "设置" 窗格中，将看到一个名为 "**参数**" 的选项卡。 选择 "**新建**" 生成新参数。 对于每个参数，您必须分配一个名称，选择一个类型，并根据需要设置默认值。

![创建数据流参数](media/data-flow/create-params.png "创建数据流参数")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在映射数据流中使用参数 

可以在任何数据流表达式中引用参数。 参数以 $ 开头并且是不可变的。 你将在 "**参数**" 选项卡下的 "表达式生成器" 中找到可用参数的列表。

![数据流参数表达式](media/data-flow/parameter-expression.png "数据流参数表达式")

可以通过选择 "**新建参数**" 并指定 "名称" 和 "类型" 来快速添加其他参数。

![数据流参数表达式](media/data-flow/new-parameter-expression.png "数据流参数表达式")

## <a name="assign-parameter-values-from-a-pipeline"></a>从管道分配参数值

创建包含参数的数据流后，可以使用 "执行数据流" 活动从管道中执行它。 将活动添加到管道画布后，会在活动的 "**参数**" 选项卡中看到可用的数据流参数。

分配参数值时，可以使用[管道表达式语言](control-flow-expression-language-functions.md)或基于 spark 类型的数据流[表达式语言](data-flow-expression-functions.md)。 每个映射数据流可以包含管道和数据流表达式参数的任意组合。

![设置数据流参数](media/data-flow/parameter-assign.png "设置数据流参数")

### <a name="pipeline-expression-parameters"></a>管道表达式参数

管道表达式参数可用于引用系统变量、函数、管道参数和类似于其他管道活动的变量。 单击 "**管道表达式**" 时，将打开一个侧导航窗格，允许您使用表达式生成器输入表达式。

![设置数据流参数](media/data-flow/parameter-pipeline.png "设置数据流参数")

引用管道参数时，将对其值进行计算，然后在数据流表达式语言中使用其值。 管道表达式类型不需要匹配数据流参数类型。 

#### <a name="string-literals-vs-expressions"></a>字符串文本与表达式

当分配 string 类型的管道表达式参数时，默认情况下将添加引号并将值作为文本进行计算。 若要读取作为数据流表达式的参数值，请选中该参数旁边的 "表达式" 框。

![设置数据流参数](media/data-flow/string-parameter.png "设置数据流参数")

如果数据流参数`stringParam`引用了值`upper(column1)`为的管道参数，则为。 

- 如果选中 expression， `$stringParam`则计算结果为 column1 的值全部为大写。
- 如果未选中 expression （默认行为）， `$stringParam`则计算结果为`'upper(column1)'`

#### <a name="passing-in-timestamps"></a>传入时间戳

在管道表达式语言中，系统变量（如`pipeline().TriggerTime` ）和函数`utcNow()` （如）以格式 "yyyy-mm-dd\'T\'HH： MM： ss" 的字符串形式返回时间戳。SSSSSSZ'. 若要将它们转换为 timestamp 类型的数据流参数，请使用字符串内插在`toTimestamp()`函数中包含所需的时间戳。 例如，若要将管道触发器时间转换为数据流参数，可以使用`toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`。 

![设置数据流参数](media/data-flow/parameter-timestamp.png "设置数据流参数")

> [!NOTE]
> 数据流只能支持最多3毫秒的数字。 `left()`函数用于修整其他数字。

#### <a name="pipeline-parameter-example"></a>管道参数示例

假设有一个整数参数， `intParam`该参数引用 String 类型的管道参数`@pipeline.parameters.pipelineParam`。 

![设置数据流参数](media/data-flow/parameter-pipeline-2.png "设置数据流参数")

`@pipeline.parameters.pipelineParam``abs(1)`在运行时为指定了值。

![设置数据流参数](media/data-flow/parameter-pipeline-4.png "设置数据流参数")

在`$intParam`表达式中引用（如派生列）时，它将计算返回`abs(1)` `1`值。 

![设置数据流参数](media/data-flow/parameter-pipeline-3.png "设置数据流参数")

### <a name="data-flow-expression-parameters"></a>数据流表达式参数

选择 "数据流**表达式**" 将打开 "数据流表达式生成器"。 您将能够在整个数据流中引用函数、其他参数和任何已定义的架构列。 引用时，此表达式的计算结果为。

> [!NOTE]
> 如果传入无效表达式或引用该转换中不存在的架构列，则该参数的计算结果将为 null。


### <a name="passing-in-a-column-name-as-a-parameter"></a>作为参数传入列名

常见的模式是将列名作为参数值传入。 如果该列是在数据流架构中定义的，则可以直接将其作为字符串表达式引用。 如果未在架构中定义列，请使用`byName()`函数。 请记住使用强制转换函数（如） `toString()`将列强制转换为其相应的类型。

例如，如果您想要基于某个参数`columnName`映射字符串列，则可以添加一个等于的派生列转换。 `toString(byName($columnName))`

![作为参数传入列名](media/data-flow/parameterize-column-name.png "作为参数传入列名")

## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
