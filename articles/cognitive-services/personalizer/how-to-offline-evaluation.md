---
title: 如何执行离线评估 - 个性化
titleSuffix: Azure Cognitive Services
description: 本文将向您展示如何使用脱机评估来衡量应用的有效性并分析学习循环。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622785"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>通过离线评估分析学习循环

了解如何完成脱机评估并理解结果。

离线评估允许您测量个性化程序与应用程序的默认行为相比的有效性，了解哪些功能对个性化贡献最大，并自动发现新的机器学习值。

请阅读[脱机评估](concepts-offline-evaluation.md)了解详细信息。

## <a name="prerequisites"></a>先决条件

* 配置的个性化器循环
* 个性化器循环必须具有具有代表性的数据量 - 作为一个球场，我们建议在其日志中至少 50，000 个事件，以获得有意义的评估结果。 （可选）还可以事先导出学习策略文件，以便在同一项评估中进行比较和测试。__

## <a name="run-an-offline-evaluation"></a>运行脱机评估

1. 在[Azure 门户](https://azure.microsoft.com/free/)中，找到个性化程序资源。
1. 在 Azure 门户中，转到 **"评估**"部分并选择 **"创建评估**"。
    ![在 Azure 门户中，转到 [评估] 部分并选择 [创建评估]。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 配置以下值：

    * 评估名称。
    * 开始日期和结束日期 - 这些是指定要在评估中使用的数据范围的日期。 此数据必须存在于日志中，如[数据保留](how-to-settings.md)值中指定的那样。
    * 优化发现设置为**是**。

    > [!div class="mx-imgBorder"]
    > ![选择脱机评估设置](./media/offline-evaluation/create-an-evaluation-form.png)

1. 通过选择 **"确定"** 开始评估。

## <a name="review-the-evaluation-results"></a>查看评估结果

运行评估可能需要花费较长时间，具体取决于要处理的数据量、要比较的学习策略数量，以及是否请求了优化。

完成后，您可以从评估列表中选择评估，然后选择将**应用程序的分数与其他潜在的学习设置进行比较**。 当您要查看当前学习策略相对于新策略的性能时，请选择此功能。

1. 回顾[学习策略](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)的表现。

    > [!div class="mx-imgBorder"]
    > [![审核评估结果](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 选择 **"应用"** 以应用最适合数据的模型的策略。

## <a name="next-steps"></a>后续步骤

* 详细了解[离线评估的工作原理](concepts-offline-evaluation.md)。
