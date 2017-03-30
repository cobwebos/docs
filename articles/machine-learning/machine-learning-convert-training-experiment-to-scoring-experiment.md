---
title: "在 Azure 机器学习中转换为预测试验 | Microsoft 文档"
description: "如何将机器学习训练实验（用于对预测分析模型定型）转换为可作为 web 服务部署的预测实验。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: db91a464843a7c2dc5460f12f7f306972d3a7da8
ms.lasthandoff: 03/22/2017


---
# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>将机器学习训练试验转换为预测试验
Azure 机器学习让用户能够生成、测试和部署预测分析解决方案。

一旦创建并在*训练实验*上重复操作以训练预测分析模型，可使用它来为新数据评分并简化评分试验。 可以随后将此*预测试验*作为 Azure Web 服务实施，以便用户可以将数据发送到模型并接收模型的预测。

通过转换为预测实验，可将训练的模型部署为 web 服务。 Web 服务的用户将输入的数据发送到模型，模型返回预测结果。 转换为预测实验后，需记住自己期望别人如何使用模型。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

将训练实验转换为预测实验的过程涉及三个步骤：

1. 保存已训练的机器学习模型，然后将机器学习算法和[训练模型][train-model]模块替换为保存的训练模型。
2. 仅对需要评分的模块的实验进行剪裁。 训练实验包括大量培训所需的模型，但是一旦模型已经过培训且可以用于评分后，则不再需要模型。
3. 定义在试验中接受 web 服务用户的数据的位置，以及返回什么样的数据。

## <a name="set-up-web-service-button"></a>设置 Web 服务按钮
运行试验（“运行”按钮位于试验画布底部），“设置 Web 服务”按钮（选择 “预测 Web 服务”选项）执行三个步骤，以便将培训试验转换为预测试验：

1. 在模块调色板（试验画布左侧）的“已训练模型”部分中，它将训练的模型另存为模块，然后将机器学习算法和[训练模型][train-model]模块替换为保存的训练模型。
2. 它会删除明显不需要的模块。 在本示例中，这包括[拆分数据][split]、<sup></sup>第二个[评分模型][score-model]和[评估模型][evaluate-model]模块。
3. 它创建 Web 服务输入和输出模块并将其添加在试验中的默认位置。

例如，以下试验使用示例人口普查数据，训练双类提升的决策树模型：

![训练实验][figure1]

在此实验的模块执行四个基本上不同的函数：

![模块函数][figure2]

将此训练实验转换为预测实验时，不再需要这些模块中的某些模块，或者它们具有不同的用途：

* **数据** - 在评分过程中不使用示例数据集中的数据 - web 服务的用户将提供要评分的数据。 但是，训练模型使用此数据集的元数据（如数据类型）。 因此，需要将数据集保存在预测试验中，以便它可以提供此元数据。
* **准备** - 根据提交的、用于评分的数据，这些模块可能或可能不需要处理传入的数据。
  
    例如，在此示例示例数据集可能具有缺失值，其包括一些不需要对模型进行训练的列。 因此[清理缺失数据][clean-missing-data]模块已包括在内，以便处理缺失值；[选择数据集中的列][select-columns]模块已包括在内，以便从数据流中排除这些额外的列。 如果知道提交的、用于评分的数没有缺失值，则可以移除[清理缺失数据][clean-missing-data]模块。 但是，由于[选择数据集中的列][select-columns]模块可帮助定义评分的函数集，需要保留该模块。
* **训练** - 这些模块用于模型的训练。 单击“设置 Web 服务”时，这些模块将被替换为单个训练模型模块。 此新模块保存在模块调色板的“训练模型”部分。
* **分数** - 在此示例中，[拆分数据][split]模块用于将数据流划分为测试数据集和定型数据集。 预测性实验不需要此模型，可将其删除。 同样，第二个<sup></sup>[评分模型][score-model]模块和[评估模型][evaluate-model]模块用于比较测试数据的结果，因此预测性实验也不需要这些模块。 然而，其余[评分模型][score-model]模块，就需要通过 Web 服务返回分数结果。

单击“设置 Web 服务”示例如下所示：

![转换预测实验][figure3]

对于装备将试验部署为 web 服务，这可能已经足够了。 但是，可能想要执行特定于试验的一些附加工作。

### <a name="adjust-input-and-output-modules"></a>调整输入和输出模块
在训练实验中，使用定型数据集，然后对其进行处理来获取窗体中机器学习算法所需的数据。 对于希望通过 Web 服务接收的数据，如果不需要上述处理，可以将“Web 服务输入模块”移动到实验中的另一个节点（单击“Web 服务输入模块”的输出，将其拖放到相应模块的输入端口）。

例如，默认情况下“设置 Web 服务”将“Web 服务输入”数据的流顶部，如上图所示。 但是，如果输入的数据不需要使用这一处理，可手动将 将“Web 服务输入”放置到数据处理模块一侧：

![移动 web 服务输入][figure4]

通过 web 服务提供的输入数据现在直接传递到分数模型模块，无需任何预处理。

相同地，默认情况下“设置 Web 服务”将“Web 服务输入”模块放置在数据的流顶部。 在此示例中，Web 服务将[评分模型][score-model]模块的输出返回给用户，其中包括完整的输入数据向量以及评分结果。

但是，若要返回与众不同的内容，则可在 **Web 服务输出** 模块之前添加其他模块。 例如，如果希望仅返回输入数据的评分结果而不是整个向量，则可添加“在数据集中选择列”[][select-columns]模块，以排除除计分结果的所有列。 然后，将“Web 服务输出”模块移动到 [选择数据集中的列][select-columns]模块的输出。 实验将如下所示：

![移动 web 服务输出][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>添加或删除其他数据处理模块
如果知道在评分过程不再需要试验中的多个模块，则可以删除这些模块。 例如，由于在数据处理模块后已将“Web 服务输入”移动到点，可以删除预测性实验中的[清理缺失数据][clean-missing-data]模块。

现在，预测试验如下所示：

![删除其他模块][figure6]

> [!TIP]
> 请注意，我们没有删除数据集或[在数据集中选择列][select-columns]模块。 Web 服务中的模型不会使用原始数据集中的数据，但使用数据集中定义的元数据，例如每个列的数据类型。 同样，[在数据集中选择列][select-columns]模块会标识数据的哪些列会由模型使用。 因此，这两个模块都需要保留在预测性实验中。

### <a name="add-optional-web-service-parameters"></a>添加其他 Web 服务参数
在某些情况下，可能希望 web 服务用户在访问该服务时可以更改模块的行为。 *Web 服务参数*允许执行此操作。

常见示例为设置[导入数据][import-data]模块，以便访问 Web 服务时，已部署的 Web 服务的用户能够指定其他数据源。 或配置[导出数据][export-data]模块以指定其他目标。

可以定义 Web 服务参数并将其与一个或多个模块参数关联，可以指定它们是否是必需或可选。 访问该服务时，Web 服务的用户可以为这些参数提供值并会对模块操作进行相应的修改。

有关 Web 服务参数的详细信息，请参阅[使用 Azure 机器学习 Web 服务参数][webserviceparameters]。

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>将预测试验外部署为 Web 服务
现在，预测试验已充分做好准备，可将其部署为 Azure web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

有关完整的部署过程的详细信息，请参阅 [Azure 机器学习 Web 服务][deploy]

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

