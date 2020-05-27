---
title: 映射数据流中的“更改行”转换
description: 如何在映射数据流中使用“更改行”转换来更新数据库目标
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982643"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>映射数据流中的“更改行”转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用“更改行”转换来设置针对行的插入、删除、更新和更新插入策略。 可以将一对多条件添加为表达式。 应按优先级顺序指定这些条件，因为每一行都将用与第一个匹配的表达式对应的策略进行标记。 其中的每个条件都可能会导致对行执行插入、更新、删除或更新插入操作。 “更改行”可能会产生对数据库的 DDL 和 DML 操作。

![“更改行”设置](media/data-flow/alter-row1.png "“更改行”设置")

“更改行”转换将仅对你的数据流中的数据库或 CosmosDB 接收器进行操作。 在调试会话期间，分配给行的操作（插入、更新、删除、更新插入）不会发生。 在管道中运行“执行数据流”活动，对数据库表执行“更改行”策略。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>指定一个默认行策略

创建一个“更改行”转换，并指定一个条件为 `true()` 的行策略。 与前面定义的任何表达式都不匹配的每个行都将标记为执行指定的行策略。 默认情况下，与任何条件表达式都不匹配的每个行都将标记为执行 `Insert`。

![“更改行”策略](media/data-flow/alter-row4.png "“更改行”策略")

> [!NOTE]
> 若要为所有行标记一个策略，可以为该策略创建一个条件，并将条件指定为 `true()`。

## <a name="view-policies-in-data-preview"></a>在数据预览中查看策略

使用[调试模式](concepts-data-flow-debug-mode.md)在数据预览窗格中查看“更改行”策略的结果。 “更改行”转换的数据预览不会针对目标产生 DDL 或 DML 操作。

![“更改行”策略](media/data-flow/alter-row3.png "“更改行”策略")

每个“更改行”策略都由一个图标表示，该图标指示是否会执行插入、更新、更新插入或删除操作。 在预览中，顶部标题会显示每个策略所影响的行数。

## <a name="allow-alter-row-policies-in-sink"></a>在接收器中允许“更改行”策略

要使“更改行”策略有效，数据流必须写入到数据库或 Cosmos 接收器。 在接收器的“设置”选项卡中，启用允许该接收器使用的“更改行”策略。

![“更改行”接收器](media/data-flow/alter-row2.png "“更改行”接收器")

默认行为是仅允许插入。 若要允许更新、更新插入或删除，请选中接收器中与该条件对应的框。 如果启用更新、更新插入或删除操作，则必须指定接收器中与之匹配的键列。

> [!NOTE]
> 如果插入、更新或更新插入操作修改了接收器中目标表的架构，则数据流会失败。 若要修改数据库中的目标架构，请选择“重新创建表”作为表操作。 这将删除表并使用新的架构定义重新创建表。

接收器转换需要单个键或一系列键才能在目标数据库中对行进行独一无二的标识。 对于 SQL 接收器，请在接收器“设置”选项卡中设置键。对于 CosmosDB，请在“设置”中设置分区键，并且还要在接收器映射中设置 CosmosDB 系统字段“id”。 对于 CosmosDB，必须包含针对更新、更新插入和删除操作的系统列“id”。

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>对 Azure SQL 数据库和 Synapse 的合并与更新插入

ADF 数据流支持使用更新插入选项对 Azure SQL 数据库和 Synapse 数据库池（数据仓库）执行合并操作。

但是，你可能会遇到目标数据库架构使用了键列的标识属性的情况。 ADF 要求你标识用来为更新和更新插入操作匹配行值的键。 但是，如果目标列设置了标识属性，而你使用的是更新插入策略，则目标数据库不会允许写入到列。 尝试对分布式表的分布列进行更新插入时，也可能会遇到错误。

下面是解决该问题的方法：

1. 转到“接收器转换设置”并设置“跳过写入键列”。 这将告诉 ADF 不要写入你选择用作映射键值的列。

2. 如果该键列不是导致标识列问题的列，则可使用此接收器转换预处理 SQL 选项：```SET IDENTITY_INSERT tbl_content ON```。 然后，通过以下后处理 SQL 属性将其关闭：```SET IDENTITY_INSERT tbl_content OFF```。

3. 对于标识案例和分布列案例，可以使用“条件拆分”转换将逻辑从更新插入切换为使用单独的更新条件和单独的插入条件。 这样即可将更新路径上的映射设置为忽略键列映射。

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

下面的示例是一个名为 `CleanData` 的“更改行”转换，它接受传入的流 `SpecifyUpsertConditions` 并创建三个“更改行”条件。 在上一转换中计算了名为 `alterRowCondition` 的列，目的是确定是否在数据库中插入、更新或删除行。 如果列的值包含与“更改行”规则匹配的字符串值，则会为其分配该策略。

在数据工厂 UX 中，此转换如下图所示：

![“更改行”示例](media/data-flow/alter-row4.png "“更改行”示例")

此转换的数据流脚本位于下面的代码片段中：

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>后续步骤

完成“更改行”转换后，即可[将数据接收到目标数据存储中](data-flow-sink.md)。
