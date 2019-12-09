---
title: 映射数据流更改行转换
description: 如何使用 Azure 数据工厂映射来更新数据库目标映射数据流更改行转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2019
ms.openlocfilehash: 1301b89ef1a6fb02356c6dcd4e568401eb5e9cd2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930483"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure 数据工厂更改行转换

使用 Alter Row 转换可对行设置 insert、delete、update 和 upsert 策略。 可以将一对多条件添加为表达式。 应按优先级顺序指定这些条件，因为每一行都将用与第一个匹配表达式对应的策略进行标记。 其中每个条件都可能会导致插入、更新、删除或 upserted 行。 Alter Row 可以为数据库生成 DDL & DML 操作。



![更改行设置](media/data-flow/alter-row1.png "更改行设置")

> [!NOTE]
> 更改行转换仅对数据流中的数据库或 CosmosDB 接收器进行操作。 在调试会话期间，分配给行（insert、update、delete、upsert）的操作将不会发生。 您必须将执行数据流任务添加到管道，并使用管道调试或触发器来对数据库表执行更改行策略。

## <a name="indicate-a-default-row-policy"></a>指示默认行策略

创建 Alter Row 转换，并指定条件为 `true()`的行策略。 每个不满足任何以前定义的表达式的行将被标记为指定的行策略。 默认情况下，每个不满足任何条件表达式的行都将被标记为 `Insert`。

![更改一行策略](media/data-flow/alter-row4.png "更改一行策略")

> [!NOTE]
> 若要将所有行标记为一个策略，可以为该策略创建条件，并将条件指定为 `true()`。

## <a name="view-policies"></a>查看策略

启用数据流调试模式，在数据预览窗格中查看更改行策略的结果。 在数据流调试模式下执行更改行不会针对目标生成 DDL 或 DML 操作。 若要执行这些操作，请在管道内的执行数据流活动中执行数据流。

![更改行策略](media/data-flow/alter-row3.png "更改行策略")

这将允许你根据条件验证和查看每一行的状态。 将在数据流中出现每个插入、更新、删除和 upsert 操作的图标，指示在管道内执行数据流时将发生的操作。

## <a name="sink-settings"></a>接收器设置

要使 Alter 行生效，您必须具有数据库接收器类型。 在接收器设置中，应将每个操作设置为要允许的更改行条件。

![更改行接收器](media/data-flow/alter-row2.png "更改行接收器")

ADF 数据流中的默认行为与数据库接收器一起插入行。 如果还想要允许更新、upsert 和删除，还必须检查接收器中的这些框以允许这些操作。

> [!NOTE]
> 如果 insert、update 或 upsert 修改了接收器中目标表的架构，则数据流将失败。 为了修改数据库中的目标架构，必须选择接收器中的 "重新创建表" 选项。 这将删除并重新创建包含新架构定义的表。

## <a name="next-steps"></a>后续步骤

更改行转换后，您可能需要将[数据接收到目标数据存储区中](data-flow-sink.md)。
