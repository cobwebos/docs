---
title: 故障排除-Personalizer
titleSuffix: Azure Cognitive Services
description: 在本文中，可以找到 Personalizer 有关的问题进行故障排除。
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722246"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 故障排除

本文包含有关 Personalizer 常见故障排除问题的答案。

## <a name="learning-loop"></a>学习循环

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学习循环似乎不了解。 如何修复此问题？

学习循环需要执行几次几千个 Reward 调用之前排名调用有效设置优先级。 

如果您不确定如何当前行为学习循环，运行[脱机评估](concepts-offline-evaluation.md)，并应用已更正的学习策略。 

## <a name="next-steps"></a>后续步骤

[配置模型更新频率](how-to-settings.md#model-update-frequency)