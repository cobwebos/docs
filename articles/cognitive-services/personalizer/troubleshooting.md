---
title: 故障排除-Personalizer
titleSuffix: Azure Cognitive Services
description: 本文包含有关 Personalizer 的常见问题的解答。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953160"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 故障排除

本文包含有关 Personalizer 的常见问题的解答。

## <a name="learning-loop"></a>学习循环

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学习循环似乎没有学习。 如何修复此问题？

学习循环需要几千个奖励调用才能有效地排定排名。 

如果不确定学习循环当前的行为方式，请运行[脱机评估](concepts-offline-evaluation.md)，并应用已更正的学习策略。 

## <a name="next-steps"></a>后续步骤

[配置模型更新频率](how-to-settings.md#model-update-frequency)