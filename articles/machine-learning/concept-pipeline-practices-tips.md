---
title: 迭代和发展机器学习管道
titleSuffix: Azure Machine Learning
description: 快速开发的模式、做法和提示
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858194"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>迭代和发展机器学习管道

Azure 机器学习管道提供了一种有效的方法来模块化代码、重用结果和优化计算资源。 下面是一些用于使用管道的实用提示和做法。

## <a name="how-do-you-get-started-with-pipelines"></a>如何开始使用管道？

如果不熟悉管道，可以通过以下几种方法入门：

* 如果通过阅读和巩固构造过程能够让你学习得更好，建议阅读[使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)一文。 
* 如果喜欢在 Jupyter 笔记本中进行交互式学习，则 [Azure 机器学习管道：入门](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb)笔记本中开发的管道可能很适合你
* 如果更喜欢优先使用代码，则可以通过克隆 [Azure 机器学习管道演示](https://github.com/microsoft/aml-pipelines-demo)存储库上手。

## <a name="how-do-you-modularize-pipeline-code"></a>如何模块化管道代码？ 

模块和 `ModuleStep` 类为模块化 ML 代码提供了绝佳的机会。 但必须记住，在管道各步骤之间移动比函数调用的成本要高得多。 必须问的问题不是“这些函数和数据的概念是否与本部分中的概念不同？”， 而是“是否希望这些函数和数据单独发展？” 或“此类计算是否昂贵以及是否可以重用其输出？” 有关详细信息，请参阅此笔记本[如何使用 ModuleStep 创建 Module、ModuleVersion 以及在管道中使用它们](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)。

如前所述，将数据准备与训练分离通常就是这样一种机会。 有时，数据准备非常复杂且十分耗时，建议将过程分解为单独的管道步骤。 其他机会包括训练后的测试和分析。 

## <a name="how-do-you-speed-pipeline-iteration"></a>如何加快管道迭代的速度？ 

用于快速迭代管道的常见方法包括： 

- 克隆管道（进行复制）并快速重新运行管道
- 保持计算实例运行，以省去启动时间
- 配置允许重用的数据和步骤将允许管道跳过重新计算没有发生改变的数据

想快速迭代时，可以克隆管道、创建管道并重新运行管道。 另一种有用的方法是，如果让计算保持待用状态，则不会产生运转新计算的成本。 如果将步骤设置为允许重用运行结果，则重复执行将尽可能（步骤中无更改时）重用结果。

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>如何使用 ML 管道进行协作？ 

分开的管道是可以分散工作量的自然线。 只要约定了步骤之间流动的数据和参数，多个开发人员甚至多个团队都可以执行不同的步骤。 

在积极开发过程中，可以从工作区中检索 `PipelineRun` 和 `StepRun` 运行结果，使用这些对象下载最终和中间输出，并将这些项目用于你自己的模块化工作。

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>使用管道测试隔离方法

现实世界中的 ML 解决方案通常涉及对每个步骤进行大量自定义。 通常需要以某种方式（筛选、转换和扩充）准备原始数据。 训练过程可能有几种潜在的体系结构，对于深度学习而言，层大小和激活函数可能有诸多变化。 即使使用一致的体系结构，超参数搜索也可以取得重大成功。

除了 [AutoML](concept-automated-ml.md) 和[自动化超参数搜索](how-to-tune-hyperparameters.md)等工具之外，管道还可以是 A/B 测试解决方案的重要工具。 如果管道步骤有多个变体，则可以很容易地生成尝试使用其变体的单独运行： 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

