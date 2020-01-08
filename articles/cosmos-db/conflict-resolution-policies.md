---
title: Azure Cosmos DB 中的冲突解决类型和解决策略
description: 本文介绍 Azure Cosmos DB 中的冲突类别和冲突解决策略。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441990"
---
# <a name="conflict-types-and-resolution-policies"></a>冲突类型和解决策略

如果为 Azure Cosmos DB 帐户配置了多个写入区域，则适用冲突和冲突解决策略的概念。

对于配置了多个写入区域的 Azure Cosmos 帐户，当编写器同时更新多个区域中的同一项时，可能会发生更新冲突。 更新冲突可以属于以下三种类型：

* **插入冲突**：当应用程序同时将两个或更多个具有相同唯一索引的项插入两个或多个区域时，会发生这些冲突。 例如，ID 属性可能会发生这种冲突。

* **替换冲突**：当应用程序在两个或多个区域中同时更新同一项时，可能会发生这些冲突。

* **删除冲突**：如果应用程序同时删除某一区域中的项并在另一个区域更新该项，则可能会发生这些冲突。

## <a name="conflict-resolution-policies"></a>冲突解决策略

Azure Cosmos DB 提供了一种灵活的策略驱动机制来解决写入冲突。 可从 Azure Cosmos 容器上的两个冲突解决策略中进行选择：

* **上次写入入选（LWW）** ：默认情况下，此解决策略使用系统定义的时间戳属性。 它基于时间同步时钟协议。 如果使用 SQL API，则可以指定要用于冲突解决的任何其他自定义数字属性（例如，你自己的时间戳概念）。 自定义数值属性也称为*冲突解决路径*。 

  如果在执行插入或替换操作时有两个或更多个项发生冲突，冲突解决路径值最大的项将成为优胜者。 如果多个项的冲突解决路径的数字值相同，则由系统确定优胜者。 保证所有区域融合到单个优胜者，并且提交的项的版本最终相同。 当涉及到删除冲突时，已删除版本始终优先于插入或替换冲突。 无论冲突解决路径的值是什么，都将发生此结果。

  > [!NOTE]
  > "上一次写入" Wins 是默认的冲突解决策略，并使用以下 Api 的时间戳 `_ts`： SQL、MongoDB、Cassandra、Gremlin 和 Table。 自定义数值属性仅可用于 SQL API。

  有关详细信息，请参阅 [LWW 冲突解决策略的用法示例](how-to-manage-conflicts.md)。

* **自定义**：此解析策略设计用于对冲突进行协调的应用程序定义的语义。 在 Azure Cosmos 容器上设置此策略时，还需要注册*合并存储过程*。 当在服务器上的数据库事务下检测到冲突时，将自动调用此过程。 在执行提交协议过程中，该系统可保证正好执行合并过程一次。  

  如果使用自定义解决方案选项配置容器，但无法在容器上注册合并过程，或者合并过程在运行时引发异常，则会将冲突写入*冲突源*。 然后，应用程序需要自行解决冲突源中的冲突。 有关详细信息，请参阅[自定义解决策略和冲突源的用法示例](how-to-manage-conflicts.md)。

  > [!NOTE]
  > 自定义冲突解决策略仅适用于 SQL API 帐户。

## <a name="next-steps"></a>后续步骤

了解如何配置冲突解决策略：

* [如何在应用程序中配置多主数据库](how-to-multi-master.md)
* [如何管理冲突解决策略](how-to-manage-conflicts.md)
* [如何从冲突源读取数据](how-to-manage-conflicts.md#read-from-conflict-feed)
