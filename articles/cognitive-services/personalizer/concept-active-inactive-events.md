---
title: 活动和非活动事件-Personalizer
description: 本文介绍如何在 Personalizer 服务中使用活动和非活动事件。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624249"
---
# <a name="active-and-inactive-events"></a>活动和非活动事件

**活动**事件是对排名的任何调用，你知道将向客户显示结果并确定奖励分数。 此选项为默认行为。

不**活动**的事件是对排名的调用，您不确定用户是否将由于业务逻辑而看到建议的操作。 这允许您放弃该事件，因此 Personalizer 不会对默认值进行训练。 非活动事件不应调用奖励 API。

学习循环应知道事件的实际类型，这一点很重要。 非活动事件不会有奖励调用。 活动事件应有一个奖励呼叫，但如果从未发出 API 调用，则会应用默认的奖励分数。 如果知道事件会影响用户体验，请立即将事件的状态从 "非活动" 更改为 "活动"。

## <a name="typical-active-events-scenario"></a>典型活动事件方案

当应用程序调用排名 API 时，你将收到操作，应用程序应在**rewardActionId**字段中显示该操作。  从那时起，Personalizer 需要一个具有相同 eventId 的奖励评分的奖励积分。 奖励分数用于为将来的排名调用定型模型。 如果没有收到对 eventId 的奖励调用，则应用默认的奖励。 [默认回报](how-to-settings.md#configure-rewards-for-the-feedback-loop)在 Azure 门户中的 Personalizer 资源上设置。

## <a name="other-event-type-scenarios"></a>其他事件类型方案

在某些情况下，应用程序可能需要先调用排名，才能知道是使用还是向用户显示结果。 例如，在某些情况下可能会发生这种情况，例如，升级内容的页面呈现被市场营销活动覆盖。 如果未使用排名调用的结果，并且用户从未看到过，则不发送相应的奖励呼叫。

通常情况下，在以下情况下会发生这些情况：

* 预呈现 UI，用户可能会或可能无法查看。
* 您的应用程序正在进行预测性的个性化，其中的排名调用使用很少的实时上下文进行，并且应用程序不能使用该输出。

在这些情况下，请使用 Personalizer 调用 Rank，请求事件处于_非活动状态_。 Personalizer 不会对此活动带来回报，也不会应用默认奖励。

稍后在业务逻辑中，如果应用程序使用排名调用中的信息，只需_激活_事件。 一旦活动处于活动状态，Personalizer 就需要一个事件回报。 如果对奖励 API 没有显式调用，则 Personalizer 将应用默认奖励。

## <a name="inactive-events"></a>非活动事件

若要禁用事件培训，请使用 `learningEnabled = False`调用排名。

对于非活动事件，如果向 eventId 发送奖励或为该 eventId 调用 `activate` API，则会隐式激活学习。

## <a name="next-steps"></a>后续步骤

* 了解[如何确定奖励评分以及要考虑的数据](concept-rewards.md)。
