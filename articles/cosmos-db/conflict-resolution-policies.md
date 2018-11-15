---
title: Azure Cosmos DB 中的冲突解决类型和解决策略
description: 本文介绍 Azure Cosmos DB 中的冲突类别和冲突解决策略。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c682b61a39224f2c80db8fe5fa153ea5e5d82922
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958532"
---
# <a name="conflict-types-and-resolution-policies"></a>冲突类型和解决策略

如果为 Cosmos 帐户配置了多个写入区域，则适用冲突和冲突解决策略的概念。

对于配置了多个写入区域的 Cosmos DB 帐户，当多个写入者同时更新多个区域中的相同项时，可能会发生更新冲突。 更新冲突划分为以下三种类型：

1. **插入冲突：** 当应用程序同时从两个或更多个区域插入具有相同唯一索引（例如 ID 属性）的两个或更多个项时，可能会发生这些冲突。 在这种情况下，相关本地区域中的所有写入最初可能会成功，但根据所选的冲突解决策略，最终只会提交一个具有原始 ID 的项。

1. **替换冲突：** 当应用程序同时从两个或更多个区域更新单个项时，可能会发生这些冲突。

1. **删除冲突：** 当应用程序同时从一个区域删除一个项并从其他区域更新该项时，可能会发生这些冲突。

## <a name="conflict-resolution-policies"></a>冲突解决策略

Cosmos DB 提供了灵活的策略驱动的机制来解决更新冲突。 可以针对 Cosmos 容器，从以下两种冲突解决策略中进行选择：

- **最后写入者胜出 (LWW)** ：此解决策略默认情况下使用系统定义的时间戳属性（基于时间同步时钟协议）。 另外，在使用 SQL API 时，Cosmos DB 允许指定其他任何自定义数字属性（也称为“冲突解决路径”）用于解决冲突。  

  如果在执行插入或替换操作时有两个或更多个项发生冲突，“冲突解决路径”值最大的项将成为“优胜者”。 如果多个项的冲突解决路径的数字值相同，则选择的“优胜者”版本由系统确定。 保证所有区域融合到单个优胜者，并且提交的项的版本最终相同。 如果涉及到删除冲突，则不管冲突解决路径的值如何，已删除版本始终优先于插入或替换冲突。

  > [!NOTE]
  > 最后写入者胜出是默认的冲突解决策略，适用于 SQL、表、MongoDB、Cassandra 和 Gremlin API 帐户。

  有关详细信息，请参阅 [LWW 冲突解决策略的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

- **自定义**：此解决策略旨在使用应用程序定义的语义来调解冲突。 针对 Cosmos 容器设置此策略时，还需要注册一个合并存储过程，在服务器上的数据库事务下检测到更新冲突时，会自动调用此存储过程。 在执行提交协议过程中，该系统可保证正好执行合并过程一次。  

  但是，如果为容器配置了自定义解决选项，但无法在容器中注册合并过程，或合并过程在运行时引发异常，则冲突将写入到冲突源。 然后，应用程序需要自行解决冲突源中的冲突。 有关详细信息，请参阅[自定义解决策略和冲突源的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

  > [!NOTE]
  > 自定义冲突解决策略仅适用于 SQL API 帐户。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章了解如何配置冲突解决策略：

* [如何使用 LWW 冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何使用自定义冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何使用冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)
