---
title: Azure 数据工厂映射数据流参数
description: 了解如何参数化映射数据流从数据工厂管道
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082880"
---
# <a name="mapping-data-flow-parameters"></a>映射数据流参数

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

将数据映射到数据工厂中的流支持使用参数。 可以在你数据的流定义，然后，可以使用整个表达式内部定义参数。 然后可以通过调用管道的执行数据流的活动通过设置参数。 可以使用数据流中的数据活动表达式设置的值的三个选项：

* 使用管道的控制流表达式语言设置动态值
* 使用数据流表达式语言设置动态值
* 使用任一表达式语言来设置静态文本值

使用此功能使你的数据的流，常规用途、 灵活且可重复使用。 你可以参数化数据的流设置和使用这些参数的表达式。

> [!NOTE]
> 若要使用管道控制流表达式，在数据流参数必须是字符串类型。

* 将执行数据流的活动添加到管道画布中。
* 如果数据的流具有参数，您将看到每个输入参数值的参数旁边的文本框中单击 tab.* * 参数中的可用参数的列表。
* 您可以选择创建管道的控制流表达式语言通过参数表达式或数据流表达式。

![数据流参数 3](media/data-flow/params3.png "数据流参数 3")

## <a name="create-parameters-in-data-flow"></a>在数据流中创建参数

![数据流参数 1](media/data-flow/params1.png "数据流参数 1")

若要将参数添加到数据的流中，单击数据流画布，若要查看的常规属性的空白部分。 在设置窗格中，将看到名为 Parameters 的选项卡。 单击新建按钮以生成新的参数，然后可以从管道中，将值传递到数据的流设置。 输入参数名称并选择每个参数的数据类型。

在你数据流表达式，可以利用使用从管道设置的值的参数。 参数以 $ 开头，并且是固定不变。 您将发现应用内表达式生成器参数选项卡下的可用参数的列表。尽管不可能将新值分配到参数，可以在表达式中使用这些值。

![数据流参数 2](media/data-flow/params2.png "数据流参数 2")

## <a name="set-data-flow-parameters-from-pipeline"></a>从管道设置数据流参数

一旦您使用参数创建数据的流，包含执行数据流活动现在从管道中执行该数据流。 一旦将该活动添加到管道设计画布，就将显示的可用数据活动的参数设置选项卡中的流参数。

![数据流参数表达式语言](media/data-flow/params4.png "数据流参数表达式语言")

当您单击填充参数值的文本框中时，您将看到数据流动表达式生成器。 在这里，可以输入任何表达式或要匹配的参数的数据类型的文字值。 以下是从表达式生成器数据流表达式和文字字符串的示例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

如果参数数据类型是一个字符串，然后你可以选择输入管道或数据流表达式。 如果您选择管道表达式，将改为显示在管道表达式面板。 请确保包括管道函数内部字符串内插语法，使用 @{<expression>}，例如：

```'@{pipeline().RunId}'```

![数据的流参数示例](media/data-flow/params5.png "数据流参数示例")

## <a name="next-steps"></a>后续步骤

* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
