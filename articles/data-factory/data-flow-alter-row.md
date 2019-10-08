---
title: Azure 数据工厂映射数据流更改行转换
description: 如何使用 Azure 数据工厂映射数据流更改行转换来更新数据库目标
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fc497837792075501bcd92f6ee07ad9ee4fe2dfa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027007"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure 数据工厂更改行转换

使用更改行转换在行上设置插入、删除、更新、更新插入策略。 可以将一对多条件作为表达式添加。 应按优先级顺序指定这些条件，因为每一行都将用与第一个匹配表达式对应的策略进行标记。 其中每个条件都可能会导致插入、更新、删除或 upserted 行。 更改行可能会对数据库执行 DDL 和 DML 操作。



![更改行设置](media/data-flow/alter-row1.png "更改行设置")

> [!NOTE]
> 更改行转换只能在数据流中的数据库接收器上操作。 分配给行的操作（插入、更新、删除、更新插入）不会在调试会话期间发生。 必须向管道添加一个执行数据流任务，并使用管道调试或触发器在数据库表上制定更改行策略。

## <a name="indicate-a-default-row-policy"></a>指示默认行策略

创建 Alter Row 转换，并指定条件为 `true()` 的行策略。 每个不满足任何以前定义的表达式的行将被标记为指定的行策略。 默认情况下，每个不满足任何条件表达式的行都将被标记为 `Insert`。

![更改行一个策略](media/data-flow/alter-row4.png "更改行策略")

> [!NOTE]
> 若要将所有行标记为一个策略，可以为该策略创建条件，并将条件指定为 `true()`。

## <a name="view-policies"></a>查看策略

启用数据流调试模式，在数据预览窗格中查看更改行策略的结果。 在“数据流调试”模式下执行更改行不会对目标进行 DDL 或 DML 操作。 若要执行这些操作，请在管道内的执行数据流活动中执行数据流。

![更改行策略](media/data-flow/alter-row3.png "更改行策略")

这样就可以根据条件验证并查看每个行的状态。 将要在数据流中发生的每个插入、更新、删除和更新插入操作都有图标符号，表示在管道中执行数据流时会发生的具体操作。

## <a name="sink-settings"></a>接收器设置

必须有一个数据库接收器类型，否则更改行无法操作。 在接收器设置中，应将每个操作设置为要允许的更改行条件。

![更改行接收器](media/data-flow/alter-row2.png "更改行接收器")

带数据库接收器的 ADF 数据流中的默认行为是插入行。 如果还希望允许更新、更新插入和删除，则还需勾选接收器中的这些框以允许相关操作。

> [!NOTE]
> 如果插入、更新或更新插入会修改接收器中的目标表的架构，则数据流会失败。 若要修改数据库中的目标架构，必须在接收器中选择“重新创建表”选项。 这样就会删除表，然后使用新的架构定义重新创建表。

## <a name="next-steps"></a>后续步骤

进行更改行转换以后，可能需要[将数据沉积到目标数据存储中](data-flow-sink.md)。
