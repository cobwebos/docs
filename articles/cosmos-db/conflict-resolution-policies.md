---
title: Azure Cosmos DB 中具有多个写入区域的冲突解决类型和解决策略
description: 本文介绍 Azure Cosmos DB 中的冲突类别和冲突解决策略。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 6b168efc2314e21f381d55f6ac79cda8b426e66d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110296"
---
# <a name="conflict-types-and-resolution-policies"></a>冲突类型和解决策略

如果为 Azure Cosmos DB 帐户配置了多个写入区域，则适用冲突和冲突解决策略的概念。

对于配置了多个写入区域的 Azure Cosmos DB 帐户，当写入者同时更新多个区域中的相同项时，可能会发生更新冲突。 更新冲突划分为以下三种类型：

* **插入冲突**：当应用程序同时从两个或更多个区域插入具有相同唯一索引的两个或更多个项时，可能会发生这些冲突。 例如，可能会因为 ID 属性而发生此冲突。 所有写入最初可能会在其各自的本地区域中成功。 但根据所选的冲突解决策略，最终只会提交一个具有原始 ID 的项目。

* **替换冲突**：当应用程序同时从两个或更多个区域更新单个项时，可能会发生这些冲突。

* **删除冲突**：当应用程序同时从一个区域删除一个项并从其他区域更新该项时，可能会发生这些冲突。

## <a name="conflict-resolution-policies"></a>冲突解决策略

Azure Cosmos DB 提供了灵活的策略驱动的机制来解决更新冲突。 可以针对 Azure Cosmos DB 容器，从以下两种冲突解决策略中进行选择：

- **最后写入者胜出 (LWW)**： 此解决策略默认情况下使用系统定义的时间戳属性。 它基于时间同步时钟协议。 若使用 Azure Cosmos DB SQL API，可以指定其他任何自定义数字属性用于解决冲突。 自定义数字属性又称为冲突解决路径。 

  如果在执行插入或替换操作时有两个或更多个项发生冲突，冲突解决路径值最大的项将成为优胜者。 如果多个项的冲突解决路径的数字值相同，则由系统确定优胜者。 保证所有区域融合到单个优胜者，并且提交的项的版本最终相同。 当涉及到删除冲突时，已删除版本始终优先于插入或替换冲突。 不管冲突解决路径的值如何，均会发生此结果。

  > [!NOTE]
  > “最后写入者胜出”是默认的冲突解决策略。 它适用于 SQL、Azure Cosmos DB 表、MongoDB、Cassandra 和 Gremlin API 帐户。

  有关详细信息，请参阅 [LWW 冲突解决策略的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

- **自定义**：此解决策略旨在使用应用程序定义的语义来调解冲突。 在 Azure Cosmos DB 容器上设置此策略时，还需注册合并存储过程。 当在服务器的数据库事务下检测到冲突时，将自动调用此过程。 在执行提交协议过程中，该系统可保证正好执行合并过程一次。  

  为容器配置该自定义解决选项时，需牢记两点。 若无法在容器中注册合并过程，或合并过程在运行时引发异常，则冲突将写入到冲突源。 然后，应用程序需要自行解决冲突源中的冲突。 有关详细信息，请参阅[自定义解决策略和冲突源的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

  > [!NOTE]
  > 自定义冲突解决策略仅适用于 SQL API 帐户。

## <a name="next-steps"></a>后续步骤

了解如何配置冲突解决策略。 请参阅以下文章：

* [如何配置应用程序中的多主数据库](how-to-multi-master.md)
* [使用 LWW 冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [使用自定义冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [使用冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)
