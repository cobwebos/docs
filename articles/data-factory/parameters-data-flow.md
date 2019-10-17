---
title: Azure 数据工厂映射数据流参数
description: 了解如何从数据工厂管道中参数化映射数据流
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387532"
---
# <a name="mapping-data-flow-parameters"></a>映射数据流参数



在 Azure 数据工厂中映射数据流支持使用参数。 你可以在数据流定义中定义参数，然后可以在整个表达式中使用这些参数。 通过执行数据流活动，可以通过调用管道设置参数值。 有三个选项可用于设置数据流活动表达式中的值：

* 使用管道控制流表达式语言设置动态值
* 使用数据流表达式语言设置动态值
* 使用任意一种表达式语言设置静态文本值

使用此功能可以使您的数据流过常规用途、灵活和可重用性。 可以用这些参数参数化数据流设置和表达式。

> [!NOTE]
> 若要使用管道控制流表达式，数据流参数的类型必须为 string。

## <a name="create-parameters-in-mapping-data-flow"></a>在映射数据流中创建参数

若要将参数添加到数据流，请单击 "数据流" 画布的空白部分以查看 "常规" 属性。 在 "设置" 窗格中，您将看到一个名为 "参数" 的选项卡。 单击 "新建" 按钮以生成新参数。 对于每个参数，您必须分配一个名称，选择一个类型，并根据需要设置默认值。

![创建数据流参数](media/data-flow/create-params.png "创建数据流参数")

可以在任何数据流表达式中使用参数。 参数以 $ 开头并且是不可变的。 您将在 "参数" 选项卡下的 "表达式生成器" 中找到可用参数的列表。

![数据流参数表达式](media/data-flow/parameter-expression.png "数据流参数表达式")

## <a name="use-parameters-in-your-data-flow"></a>在数据流中使用参数

* 您可以使用转换表达式中的参数值。 您可以在 "表达式生成器" 的 "参数" 选项卡下找到参数列表。 ![使用数据流参数](media/data-flow/params9.png "Use 数据流参数 ")

* 参数还用于为源和接收器转换设置配置动态值。 单击 "可配置" 字段内部时，将显示 "添加动态上下文" 链接。 单击此处将转到表达式生成器，可在其中使用参数来使用动态值。 ![数据流动态内容](media/data-flow/params6.png "Data flow 动态内容 ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>设置管道中的映射数据流参数

创建带参数的数据流后，可以使用 "执行数据流" 活动从管道中执行它。 将活动添加到管道画布后，会在活动的 "参数" 选项卡中显示可用的数据流参数。

![设置数据流参数](media/data-flow/parameter-assign.png "设置数据流参数")

如果您的参数数据类型为 string，则当您单击该文本框来设置参数值时，您可以选择输入管道或数据流表达式。 如果选择 "管道表达式"，将会显示 "管道表达式" 面板。 请确保将管道函数包含在使用 @no__t 0 的字符串内插语法中，例如：

```'@{pipeline().RunId}'```

如果参数不是字符串类型，则将始终向您显示 Data Flow 表达式生成器。 在此处，可以输入想要与参数的数据类型匹配的任何表达式或文本值。 下面的示例演示了表达式生成器中的数据流表达式和文本字符串：

* ```toInteger(Role)```
* ```'this is my static literal string'```

每个映射数据流可以包含管道和数据流表达式参数的任意组合。 

![数据流参数示例](media/data-flow/parameter-example.png "数据流参数示例")



## <a name="next-steps"></a>后续步骤
* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [控制流表达式](control-flow-expression-language-functions.md)
