---
title: 什么是自动化机器学习 - Azure 机器学习
description: 在本文中，可以了解自动化机器学习。 Azure 机器学习服务可以自动选取一种算法，并从中生成模型。 通过使用你提供的参数和条件为模型选择最佳算法，自动化机器学习可帮助节省时间。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960033"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

在本文中，可以了解自动化机器学习。 Azure 机器学习服务可以自动选取一种算法，并从中生成模型。 通过使用你提供的参数和条件为模型选择最佳算法，自动化机器学习可帮助节省时间。

## <a name="how-it-works"></a>工作原理

1. 你可以配置要尝试解决的机器学习问题的类型。 支持两种类别的监督式学习：
   + 分类
   + 回归

   请参阅 Azure 机器学习在培训时可以尝试的[模型列表](how-to-configure-auto-train.md#select-your-experiment-type)。

1. 指定培训数据的源和格式。 数据必须进行标记，并可存储在开发环境或 Azure Blob 存储中。 如果数据存储在开发环境中，则它必须与培训脚本位于同一目录中。 此目录将复制到为培训选择的计算目标。

    在培训脚本中，数据可以读取到 Numpy 数组或 Pandas 数据帧中。

    你可以配置拆分选项以选择培训和验证数据，也可以指定单独的培训和验证数据集。

1. 配置用于定型模型的[计算目标](how-to-set-up-training-targets.md)。

1. 配置自动化机器学习配置。 这可控制 Azure 机器学习循环访问不同模型时所使用的参数、超参数设置，以及确定最佳模型时要查看的指标。 

1. 提交培训运行。

在培训期间，Azure 机器学习服务会创建多个尝试不同算法和参数的管道。 一旦它达到提供的迭代限制，或达到所指定指标的目标值时，它将停止。

[ ![自动化机器学习](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

你可以检查记录的运行信息，其中包含运行期间收集的指标。 培训运行还会生成一个包含模型和数据预处理的 Python 序列化对象（.pkl 文件）。

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习构建模型：

+ [教程：使用 Azure 自动机器学习自动培训分类模型](tutorial-auto-train-models.md)

+ [如何为自动培训配置设置](how-to-configure-auto-train.md)

+ [如何在远程资源上使用自动培训](how-to-auto-train-remote.md) 
