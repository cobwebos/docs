---
title: 映射数据流转换概述
description: 映射数据流中可用的不同转换的概述
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086931"
---
# <a name="mapping-data-flow-transformation-overview"></a>映射数据流转换概述

下面是映射数据流中当前支持的转换的列表。 单击每个转换以了解其配置详细信息。

| “属性” | 类别 | 描述 |
| ---- | -------- | ----------- |
| [聚合](data-flow-aggregate.md) | 架构修改器 | 定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，按现有或计算列分组。 | 
| [更改行](data-flow-alter-row.md) | 行修改器 | 在行上设置插入、删除、更新和向上策略。 |
| [条件拆分](data-flow-conditional-split.md) | 多个输入/输出 | 根据匹配条件将数据行路由到不同的流。 |
| [派生列](data-flow-derived-column.md) | 架构修改器 | 使用数据流表达式语言生成新列或修改现有字段。 | 
| [存在](data-flow-exists.md) | 多个输入/输出 | 检查数据是否存在于其他源或流中。 | 
| [筛选器](data-flow-filter.md) | 行修改器 | 根据条件筛选行。 |
| [扁平 化](data-flow-flatten.md) | 架构修改器 |  在分层结构（如 JSON）中获取数组值，并将其展开到单独的行中。 |
| [联接](data-flow-join.md) | 多个输入/输出 |  合并来自两个源或流的数据。 |
| [查找](data-flow-lookup.md) | 多个输入/输出 | 来自另一个源的引用数据。 |
| [新建分支](data-flow-new-branch.md) | 多个输入/输出 | 针对同一数据流应用多组操作和转换。 |
| [支点](data-flow-pivot.md) | 架构修改器 | 一个或多个分组列具有将其不同行值转换为单个列的聚合。 |
| [选择](data-flow-select.md) | 架构修改器 | 别名列和流名称，以及删除或重新排序列 |
| [接收器](data-flow-sink.md) | - | 数据的最终目的地 |
| [排序](data-flow-sort.md) | 行修改器 | 对当前数据流上的传入行进行排序 |
| [源](data-flow-source.md) | - | 数据流的数据源 |
| [代理键](data-flow-surrogate-key.md) | 架构修改器 | 添加递增的非业务任意键值 |
| [联盟](data-flow-union.md) | 多个输入/输出 | 垂直组合多个数据流 |
| [取消支点](data-flow-unpivot.md) | 架构修改器 | 将列透视到行值 |
| [Window](data-flow-window.md) | 架构修改器 |  定义数据流中列的基于窗口的聚合。 |
