---
title: 活动和非活动事件 - 个性化
description: 本文讨论在个性化服务中使用活动和非活动事件。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624249"
---
# <a name="active-and-inactive-events"></a>活动和非活动事件

**活动**事件是任何对 Rank 的呼叫，您知道要向客户显示结果并确定奖励分数。 此选项为默认行为。

**非活动**事件是对 Rank 的调用，您不确定用户是否会由于业务逻辑而看到建议的操作。 这允许您放弃事件，因此个人化器不会使用默认奖励进行培训。 非活动事件不应调用奖励 API。

学习循环知道事件的实际类型很重要。 非活动事件将没有奖励呼叫。 活动事件应具有奖励调用，但如果从未发出 API 调用，则应用默认奖励分数。 一旦您知道事件会影响用户体验，就会立即将事件的状态从非活动更改为活动状态。

## <a name="typical-active-events-scenario"></a>典型的活动事件方案

当应用程序调用 Rank API 时，您将收到操作，应用程序应在**rewardActionId**字段中显示该操作。  从那一刻起，个人介绍者期望奖励电话的奖励分数具有相同的事件Id。 奖励分数用于训练模型以进行将来的排名呼叫。 如果未收到事件 Id 的奖励呼叫，则应用默认奖励。 [默认奖励](how-to-settings.md#configure-rewards-for-the-feedback-loop)设置在 Azure 门户中的个性化程序资源上。

## <a name="other-event-type-scenarios"></a>其他事件类型方案

在某些情况下，应用程序可能需要在知道结果是否被使用或显示给用户之前调用 Rank。 例如，升级内容的页面呈现被市场营销活动覆盖，则可能发生这种情况。 如果从未使用 Rank 调用的结果，并且用户从未看到过，请不要发送相应的奖励呼叫。

通常，在以下情况下发生这些方案：

* 您正在预呈现用户可能看到或可能不会看到的 UI。
* 应用程序正在执行预测性个性化，其中 Rank 调用很少实时上下文，应用程序可能使用也可能不使用输出。

在这些情况下，请使用"个性化器"调用 Rank，请求事件_处于非活动状态_。 个人介绍者不会期望此活动获得奖励，也不会应用默认奖励。

稍后在业务逻辑中，如果应用程序使用 Rank 调用中的信息，只需_激活_事件即可。 活动一旦活跃，个性化者就期望获得活动奖励。 如果未对奖励 API 进行显式调用，则个性化程序将应用默认奖励。

## <a name="inactive-events"></a>非活动事件

要禁用事件训练，请使用`learningEnabled = False`调用 Rank。

对于非活动事件，如果您为事件 Id 发送奖励或调用该事件Id 的`activate`API，则隐式激活学习。

## <a name="next-steps"></a>后续步骤

* [了解如何确定奖励分数以及应考虑哪些数据](concept-rewards.md)。
