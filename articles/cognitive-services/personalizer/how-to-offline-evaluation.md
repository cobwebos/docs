---
title: 如何执行脱机评估-Personalizer
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用脱机评估来度量应用的有效性并分析学习循环。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622785"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>使用脱机评估来分析学习循环

了解如何完成脱机评估并理解结果。

脱机评估允许您度量与应用程序的默认行为相比，如何将有效的 Personalizer 进行比较、了解哪些功能最适合进行个性化设置，以及自动发现新机器学习值。

请阅读[脱机评估](concepts-offline-evaluation.md)了解详细信息。

## <a name="prerequisites"></a>必备条件

* 已配置的 Personalizer 循环
* Personalizer 循环必须具有包含一定数量的数据，即大概建议在其日志中至少50000个事件，以获得有意义的评估结果。 （可选）还可以事先导出学习策略文件，以便在同一项评估中进行比较和测试。

## <a name="run-an-offline-evaluation"></a>运行脱机评估

1. 在[Azure 门户](https://azure.microsoft.com/free/)中，找到 Personalizer 资源。
1. 在 Azure 门户中，请参阅 "**评估**" 部分，并选择 "**创建评估**"。
    ![在 Azure 门户中，请参阅 "评估" 部分，并选择 "创建评估"。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 配置以下值：

    * 计算名称。
    * 开始和结束日期-这是用于指定要在计算中使用的数据范围的日期。 此数据必须出现在日志中，如[数据保持](how-to-settings.md)值中所指定。
    * 优化发现设置为 **"是"** 。

    > [!div class="mx-imgBorder"]
    > ![选择 "脱机评估设置"](./media/offline-evaluation/create-an-evaluation-form.png)

1. 通过选择 **"确定"** 开始评估。

## <a name="review-the-evaluation-results"></a>查看评估结果

运行评估可能需要花费较长时间，具体取决于要处理的数据量、要比较的学习策略数量，以及是否请求了优化。

完成后，可以从评估列表中选择评估，然后选择 "将**应用程序的分数与其他可能的学习设置进行比较"** 。 若要查看当前学习策略与新策略的对比情况，请选择此功能。

1. 查看[学习策略](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)的性能。

    > [!div class="mx-imgBorder"]
    > [![查看评估结果](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 选择 "**应用**" 以应用策略，该策略可提高数据的最佳模型。

## <a name="next-steps"></a>后续步骤

* 详细了解[脱机评估的工作方式](concepts-offline-evaluation.md)。
