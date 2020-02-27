---
title: 术语-Personalizer
description: Personalizer 使用强化学习中的术语。 这些术语用于 Azure 门户和 Api。
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624271"
---
# <a name="terminology"></a>术语

Personalizer 使用强化学习中的术语。 这些术语用于 Azure 门户和 Api。

## <a name="conceptual-terminology"></a>概念术语

* **学习循环**：为应用程序的每个部分创建名为_学习循环_的 Personalizer 资源，该资源可从个性化设置受益。 如果需要个性化多个体验，请为每个体验创建一个循环。

* **模型**： Personalizer 模型捕获所有了解用户行为的数据，从发送的参数的组合中获取定型数据以对调用进行分级和奖励，并使用由学习策略确定的定型行为。

## <a name="personalizer-configuration"></a>Personalizer 配置

Personalizer 是从[Azure 门户](https://portal.azure.com)配置的。

* **奖励**：配置奖励 wait 时间、默认奖励和奖励聚合策略的默认值。

* **浏览**：配置用于浏览的排名呼叫百分比

* **模型更新频率**：模型重新训练的频率。

* **数据保留期**：要存储的数据的天数。 这可能会影响脱机评估，这会用于改进学习循环。

## <a name="use-rank-and-reward-apis"></a>使用排名和奖励 Api

* **排名**：对于功能和上下文功能的操作，请使用 "浏览" 或 "利用" 返回 top 操作（内容项）。

    * **操作**：操作是要从中进行选择的内容项，例如产品或促销。 Personalizer 选择通过排名 API 向用户显示的顶级操作（返回的奖励操作 ID）。

    * **上下文**：若要提供更准确的排名，请提供有关上下文的信息，例如：
        * 你的用户。
        * 用户所用的设备。
        * 当前时间。
        * 有关当前情况的其他数据。
        * 有关用户或上下文的历史数据。

        特定的应用程序可能具有不同的上下文信息。

    * **[功能](concepts-features.md)** ：有关内容项或用户上下文的信息单位。 请确保仅使用聚合的功能。 不要使用特定时间、用户 Id 或其他非聚合数据作为功能。

        * _操作功能_是有关内容的元数据。
        * _上下文功能_是有关显示内容的上下文的元数据。

* **探索**： Personalizer 服务正在探索，而不是返回最佳操作，而是为用户选择不同的操作。 个性化体验创建服务可以避免偏差、停滞，并可通过探索来适应现行的用户行为。

* **利用**： Personalizer 服务使用当前模型根据过去的数据确定最佳操作。

* **试验持续时间**： Personalizer 服务等待一段时间（从该事件的排名调用开始那一刻开始）的时间量。

* **非活动事件**：非活动事件是指调用排名的事件，但由于客户端应用程序决定，用户将看不到结果。 使用非活动事件可以创建和存储个性化结果，然后决定放弃这些结果，而不会影响机器学习模型。


* **奖励**：度量值：用户如何响应排名 API 返回的奖励操作 ID，这是一个介于0到1之间的分数。 0 到 1 的值由业务逻辑设置，依据是所做的选择对实现个性化业务目标提供了多大的帮助。 学习循环不会将此奖励存储为单独的用户历史记录。

## <a name="offline-evaluations"></a>脱机评估

* **计算**：脱机评估根据循环的数据确定循环的最佳学习策略。

* **学习策略**： Personalizer 如何训练每个事件的模型将由影响机器学习算法工作方式的某些参数确定。 新的学习循环从默认的**学习策略**开始，这会产生适度的性能。 运行[评估](concepts-offline-evaluation.md)时，Personalizer 会创建专门优化到循环用例的新学习策略。 对于在评估过程中生成的每个特定循环，Personalizer 将显著提高性能。 在 Azure 门户中的 Personalizer 资源的**模型和学习设置**中，学习策略命名为 "_学习设置_"。