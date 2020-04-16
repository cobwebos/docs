---
title: 映射数据流中的架构漂移
description: 使用架构偏差在 Azure 数据工厂中生成可复原的数据流
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2019
ms.openlocfilehash: a6b3b1d11242434088c138460d968f39d5273e4f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418416"
---
# <a name="schema-drift-in-mapping-data-flow"></a>映射数据流中的架构漂移

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

架构漂移是源经常更改元数据的情况。 字段、列和 类型可以动态添加、删除或更改。 如果不处理架构漂移，数据流就容易受到上游数据源更改的影响。 当传入列和字段发生更改时，典型的 ETL 模式将失败，因为它们往往绑定到这些源名称。

为了防止架构漂移，在数据流工具中拥有这些设施非常重要，以便您作为数据工程师：

* 定义具有可变字段名称、数据类型、值和大小的源
* 定义可以使用数据模式而不是硬编码字段和值的转换参数
* 定义可以识别与传入字段相匹配而不是使用命名字段的表达式

Azure 数据工厂本机支持从执行更改为执行的灵活架构，以便无需重新编译数据流即可构建通用数据转换逻辑。

需要在数据流中做出体系结构方面的决策，使整个流接受架构偏差。 这样做可以防范源中发生架构更改。 但是，您将在整个数据流中丢失列和类型的早期绑定。 Azure 数据工厂将架构漂移流视为后期绑定流，因此，在生成转换时，漂移列名称将在整个流的架构视图中不可用。

## <a name="schema-drift-in-source"></a>架构漂移在源

来自源定义的数据流的列在源投影中不存在时定义为"漂移"。 您可以在源转换中的投影选项卡中查看源投影。 当您为源选择数据集时，ADF 将自动从数据集获取架构，并从该数据集架构定义创建项目。

在源转换中，架构漂移定义为未定义数据集架构的读取列。 要启用架构漂移，请在源转换中**检查允许架构漂移**。

![架构漂移源](media/data-flow/schemadrift001.png "架构漂移源")

启用架构漂移后，在执行期间从源读取所有传入字段，并将整个流传递到接收器。 默认情况下，所有新检测到的列（称为*漂移列*）都作为字符串数据类型到达。 如果希望数据流自动推断漂移列的数据类型，请在源设置中检查**推断漂移列类型**。

## <a name="schema-drift-in-sink"></a>架构漂移在水槽中

在接收器转换中，架构漂移是在接收器数据架构中定义的内容之上写入其他列时。 要启用架构漂移，请在接收器转换中**检查允许架构漂移**。

![架构漂移接收器](media/data-flow/schemadrift002.png "架构漂移接收器")

如果启用了架构漂移，请确保"映射"选项卡中的**自动映射**滑块已打开。 打开此滑块后，所有传入列将写入目标。 否则，必须使用基于规则的映射来编写漂移列。

![沉自动映射](media/data-flow/automap.png "沉自动映射")

## <a name="transforming-drifted-columns"></a>变换漂移列

当数据流漂移列时，可以使用以下方法在转换中访问它们：

* 使用`byPosition`和`byName`表达式按名称或位置编号显式引用列。
* 在派生列或聚合变换中添加列模式，以匹配名称、流、位置或类型的任意组合
* 在 Select 或 Sink 转换中添加基于规则的映射，以便通过模式将漂移列与列别名匹配

有关如何实现列模式的详细信息，请参阅[映射数据流的列模式](concepts-data-flow-column-pattern.md)。

### <a name="map-drifted-columns-quick-action"></a>映射漂移列快速操作

要显式引用漂移列，可以通过数据预览快速操作快速生成这些列的映射。 [启动调试模式](concepts-data-flow-debug-mode.md)后，转到"数据预览"选项卡，然后单击"**刷新"** 以获取数据预览。 如果数据工厂检测到存在漂移列，则可以单击 **"映射漂移"** 并生成派生列，允许您引用下游架构视图中的所有漂移列。

![地图漂移](media/data-flow/mapdrifted1.png "地图漂移")

在生成的派生列转换中，每个漂移列映射到其检测到的名称和数据类型。 在上述数据预览中，将检测出列"movieId"为整数。 单击 **"地图漂移**"后，movieId 在派生列`toInteger(byName('movieId'))`中定义为并包含在下游转换中的架构视图中。

![地图漂移](media/data-flow/mapdrifted2.png "地图漂移")

## <a name="next-steps"></a>后续步骤
在["数据流表达式语言](data-flow-expression-functions.md)"中，您将找到列模式和架构漂移的其他工具，包括"按名称"和"按位置"。
