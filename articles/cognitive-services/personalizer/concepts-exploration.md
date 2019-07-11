---
title: 探索 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 有了探索功能，个性化体验创建服务就能够在用户行为变化的情况下也持续提供良好的结果。 选择探索设置是一项业务决策，涉及到探索时要使用的用户交互的比例，目的是改进模型。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: ebb59b6bb7c36f4558b2bd63d2d55fa95823c4c3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722481"
---
# <a name="exploration-and-exploitation"></a>探索和开发利用

有了探索功能，个性化体验创建服务就能够在用户行为变化的情况下也持续提供良好的结果。

个性化体验创建服务在收到排名调用时，会返回一个 RewardActionID，该项可以：
* 使用开发利用功能根据当前的机器学习模型来匹配最可能的用户行为。
* 使用探索，此功能不匹配在排名中有最高可能性的操作。

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
个性化体验创建服务目前使用名为 *epsilon greedy* 的算法进行探索。 

## <a name="choosing-an-exploration-setting"></a>选择探索设置

在 Azure 门户的个性化体验创建服务的“设置”页中配置用于探索的流量的百分比。  此设置决定了执行探索的排名调用的百分比。 

个性化体验创建服务决定了是使用此概率针对每个排名调用进行探索还是进行开发利用。 这不同于某些 A/B 框架中的行为，此类框架会锁定特定用户 ID 上的处理。

## <a name="best-practices-for-choosing-an-exploration-setting"></a>选择探索设置的最佳做法

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

选择探索设置是一项业务决策，涉及到探索时要使用的用户交互的比例，目的是改进模型。 

设置为零时，会抵消个性化体验创建服务的许多优势。 使用此设置时，个性化体验创建服务不使用任何用户交互来发现更好的用户交互。 这样会导致模型停滞、偏差，并且最终会降低性能。

设置过高时，会抵消通过行为进行学习的优势。 将其设置为 100% 意味着常量随机化，不管从用户处学到什么行为，都不会影响结果。

请勿根据你所看到的个性化体验创建服务是在进行探索还是在进行开发利用来更改应用程序行为，这很重要。 这样会导致学习偏差，最终会降低潜在性能。

## <a name="next-steps"></a>后续步骤

[强化学习](concepts-reinforcement-learning.md) 