---
title: 将映射数据流参数化
description: 了解如何参数化数据工厂管道的映射数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760209"
---
# <a name="parameterizing-mapping-data-flows"></a>将映射数据流参数化

在 Azure 数据工厂中映射数据流支持参数的使用。 您可以在数据流定义中定义参数，然后在整个表达式中使用这些参数。 参数值可以通过调用管道通过执行数据流活动进行设置。 有三个选项用于设置数据流活动表达式中的值：

* 使用管道控制流表达式语言设置动态值
* 使用数据流表达式语言设置动态值
* 使用任一表达式语言设置静态文本值

使用此功能可使数据流具有通用性、灵活性和可重用性。 您可以使用这些参数参数化数据流设置和表达式。

## <a name="create-parameters-in-a-mapping-data-flow"></a>在映射数据流中创建参数

要向数据流添加参数，请单击数据流画布的空白部分以查看常规属性。 在"设置"窗格中，您将看到一个名为 **"参数**"的选项卡。 选择 **"新建"** 以生成新参数。 对于每个参数，必须指定名称、选择类型，并选择设置默认值。

![创建数据流参数](media/data-flow/create-params.png "创建数据流参数")

## <a name="use-parameters-in-a-mapping-data-flow"></a>在映射数据流中使用参数 

参数可以在任何数据流表达式中引用。 参数以 $ 开头，不可变。 您可以在"**参数"** 选项卡下找到表达式生成器内部可用参数的列表。

![数据流参数表达式](media/data-flow/parameter-expression.png "数据流参数表达式")

您可以通过选择 **"新建"参数**并指定名称和类型来快速添加其他参数。

![数据流参数表达式](media/data-flow/new-parameter-expression.png "数据流参数表达式")

### <a name="passing-in-a-column-name-as-a-parameter"></a>将列名称作为参数传递

常见的模式是将列名称作为参数值传递。 要引用与参数关联的列，请使用 函数`byName()`。 请记住，使用强制转换函数（如`toString()`）将列转换为其适当类型。

例如，如果要基于参数`columnName`映射字符串列，则可以添加等于`toString(byName($columnName))`的派生列变换。

![将列名称作为参数传递](media/data-flow/parameterize-column-name.png "将列名称作为辅助项传递")

## <a name="assign-parameter-values-from-a-pipeline"></a>从管道分配参数值

使用参数创建数据流后，可以使用执行数据流活动从管道执行数据流。 将活动添加到管道画布后，将在活动的 **"参数"** 选项卡中显示可用数据流参数。

![设置数据流参数](media/data-flow/parameter-assign.png "设置数据流参数")

如果参数数据类型为字符串，则当您单击文本框以设置参数值时，可以选择输入管道或数据流表达式。 如果选择管道表达式，将显示管道表达式面板。 请确保在字符串插值语法中包含管道函数，`'@{<expression>}'`例如：

```'@{pipeline().RunId}'```

如果参数不是类型字符串，则始终将显示数据流表达式生成器。 在这里，您可以输入您希望与参数数据类型匹配的任何表达式或文本值。 下面是数据流表达式的示例和表达式生成器的字面字符串：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每个映射数据流可以具有管道和数据流表达式参数的任意组合。 

![数据流参数示例](media/data-flow/parameter-example.png "数据流参数示例")



## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
