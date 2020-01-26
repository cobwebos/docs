---
title: 将映射数据流参数化
description: 了解如何从数据工厂管道中参数化映射数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760209"
---
# <a name="parameterizing-mapping-data-flows"></a>将映射数据流参数化

在 Azure 数据工厂中映射数据流支持使用参数。 你可以在数据流定义中定义参数，然后可以在整个表达式中使用这些参数。 通过执行数据流活动，可以通过调用管道设置参数值。 有三个选项可用于设置数据流活动表达式中的值：

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

### <a name="passing-in-a-column-name-as-a-parameter"></a>作为参数传入列名

常见的模式是将列名作为参数值传入。 若要引用与参数关联的列，请使用 `byName()` 函数。 请记住使用强制转换函数（如 `toString()`）将列强制转换为其相应的类型。

例如，如果想要根据参数 `columnName`映射字符串列，则可以添加一个等于 `toString(byName($columnName))`的派生列转换。

![作为参数传入列名](media/data-flow/parameterize-column-name.png "作为 language 传入列名")

## <a name="assign-parameter-values-from-a-pipeline"></a>从管道分配参数值

创建带参数的数据流后，可以使用 "执行数据流" 活动从管道中执行它。 将活动添加到管道画布后，会在活动的 "**参数**" 选项卡中看到可用的数据流参数。

![设置数据流参数](media/data-flow/parameter-assign.png "设置数据流参数")

如果您的参数数据类型为 string，则当您单击该文本框来设置参数值时，您可以选择输入管道或数据流表达式。 如果选择 "管道表达式"，将会显示 "管道表达式" 面板。 请确保在使用 `'@{<expression>}'`的字符串内插语法中包含管道函数，例如：

```'@{pipeline().RunId}'```

如果参数不是字符串类型，则将始终向您显示 Data Flow 表达式生成器。 在此处，可以输入想要与参数的数据类型匹配的任何表达式或文本值。 下面的示例演示了表达式生成器中的数据流表达式和文本字符串：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每个映射数据流可以包含管道和数据流表达式参数的任意组合。 

![数据流参数示例](media/data-flow/parameter-example.png "数据流参数示例")



## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
