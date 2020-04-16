---
title: 在映射数据流中更改行转换
description: 如何在映射数据流中使用更改行转换更新数据库目标
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 2923e087426ee04c74da629f4e2d2d49a06eb1ef
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416533"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>在映射数据流中更改行转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用更改行转换在行上设置插入、删除、更新、更新插入策略。 可以将一对多条件作为表达式添加。 这些条件应按优先级顺序指定，因为每行都将用与第一个匹配表达式对应的策略进行标记。 每个条件都可能导致插入、更新、删除或向上更新行（或行）。 更改行可能会对数据库执行 DDL 和 DML 操作。

![更改行设置](media/data-flow/alter-row1.png "更改行设置")

更改行转换将仅在数据流中的数据库或 CosmosDB 接收器上运行。 在调试会话期间，不会执行分配给行的操作（插入、更新、删除、向上更新）。 在管道中运行执行数据流活动，以在数据库表上执行更改行策略。

## <a name="specify-a-default-row-policy"></a>指定默认行策略

创建"更改行"转换并指定条件为 的`true()`行策略。 将为每个与以前定义的表达式不匹配的行进行标记。 默认情况下，与任何条件表达式不匹配的每行都将标记为`Insert`。

![更改行策略](media/data-flow/alter-row4.png "更改行策略")

> [!NOTE]
> 要用一个策略标记所有行，可以为该策略创建条件，并将条件指定为`true()`。

## <a name="view-policies-in-data-preview"></a>在数据预览中查看策略

使用[调试模式](concepts-data-flow-debug-mode.md)在数据预览窗格中查看更改行策略的结果。 更改行转换的数据预览不会针对目标生成 DDL 或 DML 操作。

![更改行策略](media/data-flow/alter-row3.png "更改行策略")

每个更改行策略都由一个图标表示，该图标指示是否将发生插入、更新、向上或已删除的操作。 顶部标题显示预览中每个策略受影响的行数。

## <a name="allow-alter-row-policies-in-sink"></a>允许在接收器中更改行策略

要使更改行策略正常工作，数据流必须写入数据库或 Cosmos 接收器。 在接收器中的 **"设置"** 选项卡中，启用允许该接收器更改行策略的。

![更改行接收器](media/data-flow/alter-row2.png "更改行接收器")

 默认行为是只允许插入。 要允许更新、升级或删除，请在接收器中选中与该条件对应的框。 如果启用了更新、升级或删除，则必须指定要匹配的接收器中的键列。

> [!NOTE]
> 如果插入、更新或 upserts 修改接收器中目标表的架构，数据流将失败。 要修改数据库中的目标架构，请选择 **"重新创建表**"作为表操作。 这样就会删除表，然后使用新的架构定义重新创建表。

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

下面的示例是一个命名`CleanData`更改行转换，该转换采用传入`SpecifyUpsertConditions`流并创建三个更改行条件。 在上一个转换中，将`alterRowCondition`计算一个命名的列，该列确定在数据库中是否插入、更新或删除行。 如果列的值具有与 alter 行规则匹配的字符串值，则为其分配该策略。

在数据工厂 UX 中，此转换类似于下图：

![更改行示例](media/data-flow/alter-row4.png "更改行示例")

此转换的数据流脚本位于下面的代码段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>后续步骤

进行更改行转换以后，可能需要[将数据沉积到目标数据存储中](data-flow-sink.md)。
