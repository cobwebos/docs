---
title: 映射数据流中的架构偏差 |Azure 数据工厂
description: 使用架构偏差在 Azure 数据工厂中生成可复原的数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8c2764535515d0aeb1eb65a1621148fa58317cac
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553696"
---
# <a name="schema-drift-in-mapping-data-flow"></a>映射数据流中的架构偏差

通常情况下，源经常更改元数据。 可以动态添加、删除或更改字段、列和类型。 如果不处理架构偏移，数据流就会受到上游数据源更改的影响。 当传入的列和字段发生更改时，典型的 ETL 模式将失败，因为它们往往绑定到这些源名称。

若要防止架构偏移，请务必让 data flow 工具中的工具允许你作为数据工程人员：

* 定义具有可变字段名称、数据类型、值和大小的源
* 定义可以使用数据模式而不是硬编码字段和值的转换参数
* 定义可以识别与传入字段相匹配而不是使用命名字段的表达式

Azure 数据工厂以本机方式支持灵活的架构，这些架构从执行更改为执行，这样您就无需重新编译数据流即可构建泛型数据转换逻辑。

需要在数据流中做出体系结构方面的决策，使整个流接受架构偏差。 这样做可以防范源中发生架构更改。 但是，在整个数据流中，将丢失列和类型的早期绑定。 Azure 数据工厂将架构偏差处理为后期绑定流，因此，在生成转换时，在整个流的架构视图中，偏移列的名称将不可用。

## <a name="schema-drift-in-source"></a>源中的架构偏差

从源定义进入数据流的列在源投影中不存在时定义为 "偏移"。 您可以从源转换的 "投影" 选项卡中查看源投影。 为源选择数据集时，ADF 会自动从数据集中获取架构，并通过该数据集架构定义创建项目。

在源转换中，架构偏移定义为读取未定义数据集架构的列。 若要启用架构偏移，请选中 "允许在源转换中使用**架构偏移**"。

![架构偏差源](media/data-flow/schemadrift001.png "架构偏差源")

启用架构偏差后，将在执行过程中从源读取所有传入字段，并将整个流传递到接收器。 默认情况下，所有新检测到的列（称为*偏移列*）将作为字符串数据类型到达。 如果希望数据流自动推断偏移列的数据类型，请选中 "在源设置中**推断偏移列类型**"。

## <a name="schema-drift-in-sink"></a>接收器中的架构偏差

在接收器转换中，当你在接收器数据架构中定义的列的顶层编写其他列时，架构偏移就是。 若要启用架构偏移，请选中 "在接收器转换中**允许架构偏移**"。

![架构偏移接收器](media/data-flow/schemadrift002.png "架构偏移接收器")

如果启用了架构偏移，请确保 "映射" 选项卡中的 "**自动映射**" 滑块处于打开状态。 在上打开此滑块时，所有传入列都将写入您的目标。 否则，必须使用基于规则的映射来编写偏移列。

![接收器自动映射](media/data-flow/automap.png "接收器自动映射")

## <a name="transforming-drifted-columns"></a>转换偏移列

当数据流包含偏移列时，可以使用以下方法在转换中访问它们：

* 使用 `byPosition` 和 `byName` 表达式按名称或位置号显式引用列。
* 添加派生列或聚合转换中的列模式，以匹配任何名称、流、位置或类型的组合
* 在 Select 或 Sink 转换中添加基于规则的映射，以便通过模式将偏移列与列别名匹配

有关如何实现列模式的详细信息，请参阅[映射数据流中的列模式](concepts-data-flow-column-pattern.md)。

### <a name="map-drifted-columns-quick-action"></a>Map 偏移列快速操作

若要显式引用偏移列，可以通过数据预览快速操作快速生成这些列的映射。 启用[调试模式](concepts-data-flow-debug-mode.md)后，请进入 "数据预览" 选项卡，然后单击 "**刷新**" 以获取数据预览。 如果数据工厂检测到偏移列存在，则可以单击**Map 偏移**并生成一个派生列，使您可以在下游的架构视图中引用所有偏移列。

![地图偏移](media/data-flow/mapdrifted1.png "地图偏移")

在生成的派生列转换中，每个偏移列都映射到其检测到的名称和数据类型。 在上面的数据预览中，将 "movieId" 列检测为一个整数。 单击**Map 偏移**后，将在派生列中将 movieId 定义为 `toInteger(byName('movieId'))`，并将其包含在下游转换的架构视图中。

![地图偏移](media/data-flow/mapdrifted2.png "地图偏移")

## <a name="next-steps"></a>后续步骤
在[数据流表达式语言](data-flow-expression-functions.md)中，你将找到用于列模式和架构偏移的其他功能，包括 "byName" 和 "byPosition"。
