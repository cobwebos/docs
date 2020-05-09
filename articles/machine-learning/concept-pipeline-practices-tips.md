---
title: 迭代和发展机器学习管道
titleSuffix: Azure Machine Learning
description: 快速开发的模式、实践和提示
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858194"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>迭代和发展机器学习管道

Azure 机器学习管道提供了一种有效的方法来模块化代码、重复使用结果和优化计算资源。 下面是一些处理管道的实用提示和实践。

## <a name="how-do-you-get-started-with-pipelines"></a>如何开始处理管道？

如果你不熟悉管道，可通过以下几个选项开始：

* 如果通过阅读并重新创建构造过程来了解最大程度，文章[使用 AZURE 机器学习 SDK 来创建和运行机器学习管道](how-to-create-your-first-pipeline.md)是非常适合的 
* 如果你喜欢以交互方式在 Jupyter 笔记本中学习，则 Azure 机器学习管道中开发的管道[：入门](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb)笔记本可能适合你
* 如果喜欢使用代码优先的情况，则克隆[Azure 机器学习管道演示](https://github.com/microsoft/aml-pipelines-demo)存储库提供了一个很好的起点

## <a name="how-do-you-modularize-pipeline-code"></a>如何模块化管道代码？ 

模块和`ModuleStep`类提供了模块化 ML 代码的极好机会。 但是，必须记住，在管道步骤之间移动比函数调用要大得多。 您必须问的问题并不太多？这些函数和数据在概念上不同于本部分中的那些函数和数据？ " 但 "我想要这些函数和数据单独发展吗？" 或 "这种计算开销高昂，并且我可以重复使用其输出吗？" 有关详细信息，请参阅 thisn'tebook how [to Create Module，ModuleVersion，并在具有 ModuleStep 的管道中使用它们](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)。

如前所述，分离培训的数据准备通常是一种机会。 有时，数据准备工作非常复杂且非常耗时，因此可能会将该过程分解为单独的管道步骤。 其他机会包括培训后的测试和分析。 

## <a name="how-do-you-speed-pipeline-iteration"></a>如何加速管道迭代？ 

快速循环访问管道的常见方法包括： 

- 克隆管道（生成复制）并快速重新运行管道
- 保持计算实例的运行状态，以便避免启动时间
- 配置数据和允许重复使用的步骤将允许管道跳过重新计算不变的数据

当你想要快速循环访问时，可以克隆管道、建立管道并重新运行管道。 另一种有用的方法是，如果你让计算更热，则不会产生加速新计算的成本。 如果设置了允许重复运行结果的步骤，则重复执行将尽可能重复使用结果（在步骤中没有更改时）。

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>如何使用 ML 管道进行协作？ 

分隔管道是用于拆分工作量的自然线。 多个开发人员甚至多个团队可以处理不同的步骤，前提是这些步骤之间的数据和参数在达成一致。 

在活动开发过程中，您`PipelineRun`可以`StepRun`从工作区检索和运行结果，使用这些对象下载最终和中间输出，并将这些项目用于您自己的模块化工作。

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>使用管道来测试隔离的技术

实际的 ML 解决方案通常涉及每个步骤的相当大的自定义。 原始数据通常需要以某种方式进行准备：筛选、转换和扩充。 训练过程可能有多种可能的体系结构，对于深度学习，层大小和激活函数可能有很多变化。 即使使用一致的体系结构，超参数搜索也可能产生大量的 wins。

除了[AutoML](concept-automated-ml.md)和[自动超参数搜索](how-to-tune-hyperparameters.md)之类的工具，管道对于 A/B 测试解决方案也是一个重要的工具。 如果你有多个管道步骤的变体，则可以轻松地生成单独的运行，尝试其变体： 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

