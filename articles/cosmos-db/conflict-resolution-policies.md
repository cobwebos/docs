---
title: Azure Cosmos DB 中具有多个写入区域的冲突解决类型和解决策略
description: 本文介绍 Azure Cosmos DB 中的冲突类别和冲突解决策略。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407413"
---
# <a name="conflict-types-and-resolution-policies"></a>冲突类型和解决策略

如果为 Azure Cosmos DB 帐户配置了多个写入区域，则适用冲突和冲突解决策略的概念。

对于 Azure Cosmos 帐户配置了多个写入区域，编写器同时更新多个区域中的相同项时，会发生更新冲突。 更新冲突可以是以下三种类型之一：

* **插入冲突**：应用程序同时在两个或多个区域中插入具有相同的唯一索引的两个或多个项时，会发生这些冲突。 例如，具有 ID 属性可能会发生此冲突。

* **替换冲突**：当应用程序更新同一项同时在两个或多个区域中的，会发生这些冲突。

* **删除冲突**：当应用程序同时删除一个区域中的项，并在另一个区域中对其进行更新，会发生这些冲突。

## <a name="conflict-resolution-policies"></a>冲突解决策略

Azure Cosmos DB 提供灵活策略驱动的机制来解决写入冲突。 您可以选择从 Azure Cosmos 容器上的两种冲突解决策略：

- **最后写入者胜出 (LWW)**：此解决策略默认情况下使用系统定义的时间戳属性。 它基于时间同步时钟协议。 如果使用 SQL API，可以指定任何其他自定义数字属性 （例如，你自己的时间戳的概念） 要用于冲突解决方法。 自定义的数值属性也称为*冲突解析路径*。 

  如果在执行插入或替换操作时有两个或更多个项发生冲突，冲突解决路径值最大的项将成为优胜者。 如果多个项的冲突解决路径的数字值相同，则由系统确定优胜者。 保证所有区域融合到单个优胜者，并且提交的项的版本最终相同。 当涉及到删除冲突时，已删除版本始终优先于插入或替换冲突。 无论冲突解析路径的值是什么会发生这种情况。

  > [!NOTE]
  > “最后写入者胜出”是默认的冲突解决策略。 它现在可供以下 Api:SQL、 MongoDB、 Cassandra、 Gremlin 和表。

  有关详细信息，请参阅 [LWW 冲突解决策略的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

- **自定义**：此解决策略旨在使用应用程序定义的语义来调解冲突。 Azure Cosmos 容器上设置了此策略，还需要注册*合并存储的过程*。 在服务器上的数据库事务下检测到冲突时，会自动调用此过程。 在执行提交协议过程中，该系统可保证正好执行合并过程一次。  

  如果使用自定义解决方法选项中，配置您的容器和无法注册容器上的合并过程或合并过程在运行时将引发异常，将写入冲突*冲突源*。 然后，应用程序需要自行解决冲突源中的冲突。 有关详细信息，请参阅[自定义解决策略和冲突源的用法示例](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)。

  > [!NOTE]
  > 自定义冲突解决策略仅适用于 SQL API 帐户。

## <a name="next-steps"></a>后续步骤

了解如何配置冲突解决策略：

* [如何在应用程序中配置多主数据库](how-to-multi-master.md)
* [如何使用 LWW 冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何使用自定义冲突解决策略](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [如何读取冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)
