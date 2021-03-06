---
title: 如何执行脱机评估-Personalizer
titleSuffix: Azure Cognitive Services
description: 了解如何使用脱机评估分析学习循环
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: bd57880b11f56b13b4225652071593d29dcc6280
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515225"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>使用脱机评估来分析学习循环

了解如何完成脱机评估并理解结果。

使用脱机评估可以衡量个性化体验创建服务相比应用程序默认行为的有效性，了解哪些特征对个性化的作用最大，并自动发现新的机器学习设置。

请阅读[脱机评估](concepts-offline-evaluation.md)了解详细信息。


## <a name="prerequisites"></a>必备组件

* 已配置的 Personalizer 循环
* Personalizer 循环必须具有包含一定数量的数据，即大概建议在其日志中至少50000个事件，以获得有意义的评估结果。 （可选）还可以事先导出学习策略文件，以便在同一项评估中进行比较和测试。

## <a name="steps-to-start-a-new-offline-evaluation"></a>启动新的脱机评估的步骤

1. 在[Azure 门户](https://azure.microsoft.com/free/)中，找到个性化资源。
1. 在 Azure 门户中，请参阅 "**评估**" 部分，并选择 "**创建评估**"。
    ![In Azure 门户，请参阅 "评估" 部分，并选择 "创建评估"。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 选择以下设置：

    * 计算名称
    * 开始和结束日期-这是过去的日期，用于指定要在计算中使用的数据的范围。 这些数据必须根据[数据保留期](how-to-settings.md)设置中的指定存在于日志中。
    * 优化发现设置为 **"是"**

    ![选择脱机评估设置](./media/offline-evaluation/create-an-evaluation-form.png)

1. 通过选择 **"确定"** 开始评估。 

## <a name="results"></a>结果

运行评估可能需要花费较长时间，具体取决于要处理的数据量、要比较的学习策略数量，以及是否请求了优化。

完成后，可以从评估列表中选择评估。 

学习策略的比较包括：

* **联机策略**： Personalizer 中使用的当前学习策略
* **基线**：应用程序的默认值（由排名调用中发送的第一个操作确定），
* **随机策略**：一个虚排名行为，始终从提供的操作返回随机选择的操作。
* **自定义策略**：启动评估时上传的其他学习策略。
* **优化策略**：如果计算是使用发现优化策略的选项启动的，则还将进行比较，并且你将能够下载它或使其成为联机学习策略，并替换当前的策略。

![脱机计算设置的结果图表](./media/offline-evaluation/evaluation-results.png)

操作和上下文[特征](concepts-features.md)的有效性。

## <a name="next-steps"></a>后续步骤

* 了解[脱机评估的工作原理](concepts-offline-evaluation.md)。
