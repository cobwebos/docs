---
title: Azure 数据工厂将数据流参数映射
description: 了解如何参数化映射数据流从数据工厂管道
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a7140f70db78c8511f3c4da00b2f9c11c368163
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477697"
---
# <a name="mapping-data-flow-parameters"></a>映射数据流参数

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

映射 Azure 数据工厂中数据的流支持使用参数。 可以在你数据的流定义，然后，可以使用整个表达式内部定义参数。 可以通过调用管道的执行数据流的活动通过设置参数值。 可以使用活动表达式数据流中的数据设置值的三个选项：

* 使用管道的控制流表达式语言设置动态值
* 使用数据流表达式语言设置动态值
* 使用任一表达式语言来设置静态文本值

使用此功能使你的数据的流，常规用途、 灵活且可重复使用。 你可以参数化数据的流设置和使用这些参数的表达式。

> [!NOTE]
> 若要使用管道控制流表达式，在数据流参数必须是字符串类型。

## <a name="create-parameters-in-mapping-data-flow"></a>在映射数据流中创建参数

若要将参数添加到数据的流中，单击数据流画布，若要查看的常规属性的空白部分。 在设置窗格中，将看到名为参数选项卡。 单击新建按钮以生成一个新的参数。 对于每个参数，必须分配一个名称，选择一个类型，并 （可选） 设置默认值。

![创建数据流参数](media/data-flow/create-params.png "创建数据流参数")

参数可以利用任何数据流表达式中。 参数以 $ 开头，并且是固定不变。 您将发现内部表达式生成器参数选项卡下的可用参数的列表。

![数据流参数表达式](media/data-flow/parameter-expression.png "数据流参数表达式")

## <a name="use-parameters-in-your-data-flow"></a>在数据流中使用参数

* 可以在转换表达式内使用参数值。 表达式生成器中，您会发现参数选项卡下的参数列表。 ![使用数据流参数](media/data-flow/params9.png "使用数据流参数")

* 此外可以使用参数动态值配置为源和接收器转换设置。 在可配置字段内单击时，您将看到显示的"添加动态内容"链接。 单击存在将转到一个表达式生成器，您可以使用参数的位置以使用动态值。 ![数据流动态内容](media/data-flow/params6.png "数据流动态内容")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>从管道设置映射数据流参数

一旦您使用参数创建数据的流，您可以执行它从管道包含执行数据流活动。 将活动添加到管道画布后，您将显示可用数据活动的参数选项卡中的流参数。

![设置数据流参数](media/data-flow/parameter-assign.png "数据流参数设置")

如果参数数据类型是字符串，当您单击文本框中来设置参数值时，你可以选择输入管道或数据流表达式。 如果选择管道表达式，则将显示与管道表达式面板。 请确保包括管道函数内部字符串内插语法，使用 @{<expression>}，例如：

```'@{pipeline().RunId}'```

如果参数不是字符串类型，则将始终显示与数据流表达式生成器。 在这里，可以输入任何表达式或你想参数的数据类型匹配的文本值。 以下是从表达式生成器数据流表达式和文字字符串的示例：

* ```toInteger(Role)```
* ```'this is my static literal string'```

可以为每个映射的数据流管道和数据的流表达式参数的任意组合。 

![数据的流参数示例](media/data-flow/parameter-example.png "数据流参数示例")



## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
