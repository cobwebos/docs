---
title: 评审终结点陈述以在语言理解 (LUIS) 中使用主动学习 - Azure | Microsoft Docs
description: 使用名为“评审终结点陈述”的主动学习功能加快性能预测。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35371089"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>通过评审终结点陈述启用主动学习
主动学习是提高预测准确性的三个策略之一，也是最易于实现的策略。 

## <a name="what-is-active-learning"></a>什么是主动学习
主动学习是一个两步过程。 首先，LUIS 选择其在应用终结点收到的需要验证的陈述。 第二步由应用所有者或协作者执行：验证要[评审](label-suggested-utterances.md)的所选陈述包含正确意向及该意向中的所有实体。 评审陈述后，再次训练并发布应用。 

## <a name="which-utterances-are-on-the-review-list"></a>评审列表上有哪些陈述
首要触发意向分数较低或者两个最高的意向分数过于接近时，LUIS 会将陈述添加到评审列表。 

## <a name="where-are-the-utterances-from"></a>陈述来自哪里
终结点陈述来自应用程序 HTTP 终结点上的最终用户查询。 如果未发布应用或应用未收到点击，则没有要评审的陈述。 如果未收到针对特定意向或实体的终结点点击，则没有包含它们的陈述需要进行评审。 

## <a name="schedule-review-periodically"></a>定期计划评审
不需要每天评审建议的陈述，但应将此纳入 LUIS 的常规维护。 

## <a name="delete-review-items-programmatically"></a>以编程方式删除评审项
如果应用较大，可选择评审一些陈述，并以编程方式删除列表中的剩余陈述。 要想执行此操作，首先需[获取](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a)列表，然后按 ID [删除](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)陈述。

## <a name="next-steps"></a>后续步骤

* 了解如何[评审](Label-Suggested-Utterances.md)终结点陈述
