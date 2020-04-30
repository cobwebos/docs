---
title: 在映射数据流中更改行转换
description: 如何在映射数据流中使用 alter row 转换来更新数据库目标
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/20/2020
ms.openlocfilehash: 6b353967c9b9c7517f1a42581717c6394c0e6374
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729136"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>在映射数据流中更改行转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用更改行转换在行上设置插入、删除、更新、更新插入策略。 可以将一对多条件作为表达式添加。 应按优先级顺序指定这些条件，因为每一行都将用与第一个匹配表达式对应的策略进行标记。 其中每个条件都可能会导致插入、更新、删除或 upserted 行。 更改行可能会对数据库执行 DDL 和 DML 操作。

![更改行设置](media/data-flow/alter-row1.png "更改行设置")

更改行转换仅对数据流中的数据库或 CosmosDB 接收器进行操作。 在调试会话期间，分配给行（insert、update、delete、upsert）的操作不会发生。 在管道中运行 "执行数据流" 活动，以对数据库表执行更改行策略。

## <a name="specify-a-default-row-policy"></a>指定默认行策略

创建 Alter Row 转换，并指定条件为的`true()`行策略。 每个与先前定义的表达式都不匹配的行将被标记为指定的行策略。 默认情况下，不匹配任何条件表达式的每一行都将标记`Insert`为。

![更改行策略](media/data-flow/alter-row4.png "更改行策略")

> [!NOTE]
> 若要将所有行标记为一个策略，可以为该策略创建一个条件，并将条件`true()`指定为。

## <a name="view-policies-in-data-preview"></a>在数据预览中查看策略

使用 "[调试模式](concepts-data-flow-debug-mode.md)" 在 "数据预览" 窗格中查看更改行策略的结果。 更改行转换的数据预览不会针对目标生成 DDL 或 DML 操作。

![更改行策略](media/data-flow/alter-row3.png "更改行策略")

每个更改行策略都由一个图标表示，该图标指示是否将执行插入、更新、upsert 或删除操作。 顶部标题显示了预览中的每个策略所影响的行数。

## <a name="allow-alter-row-policies-in-sink"></a>允许在接收器中更改行策略

要使更改行策略正常工作，数据流必须写入数据库或 Cosmos 接收器。 在接收器的 "**设置**" 选项卡中，启用该接收器允许哪些 alter row 策略。

![更改行接收器](media/data-flow/alter-row2.png "更改行接收器")

默认行为是仅允许插入。 若要允许更新、upsert 或删除，请选中与该条件对应的接收器中的框。 如果启用更新、upsert 或，则必须指定接收器中要匹配的键列。

> [!NOTE]
> 如果 insert、update 或 upsert 修改了接收器中目标表的架构，数据流将失败。 若要修改数据库中的目标架构，请选择 "**重新创建表**" 作为表操作。 这样就会删除表，然后使用新的架构定义重新创建表。

接收器转换需要使用单个键或一系列键来确定目标数据库中的唯一行标识。 对于 SQL 接收器，在 "接收器设置" 选项卡中设置密钥。对于 CosmosDB，请在设置中设置分区键，同时在接收器映射中设置 CosmosDB 系统字段 "id"。 对于 CosmosDB，必须为更新、upsert 和删除包含系统列 "id"。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个名为`CleanData`的更改行转换，它采用`SpecifyUpsertConditions`传入流并创建三个更改行条件。 在上一转换中，将计算`alterRowCondition`名为的列，以确定是否在数据库中插入、更新或删除行。 如果列的值有一个与 alter row 规则匹配的字符串值，则会为其分配该策略。

在数据工厂 UX 中，此转换如下图所示：

![更改行示例](media/data-flow/alter-row4.png "更改行示例")

此转换的数据流脚本位于下面的代码片段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>后续步骤

进行更改行转换以后，可能需要[将数据沉积到目标数据存储中](data-flow-sink.md)。
