---
title: 为部署准备模型
titleSuffix: ML Studio (classic) Azure
description: 如何通过将机器学习 Studio （经典）定型实验转换为预测实验，来准备要部署为 web 服务的训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e24393783dac0f918009f3138f31bdda98bbd22e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684858"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>如何在 Azure 机器学习 Studio （经典）中准备要部署的模型

Azure 机器学习 Studio （经典）提供开发预测分析模型所需的工具，然后将其部署为 Azure web 服务操作。

为此，请使用经典版本的 Studio 创建一个试验，即训练*试验*-您在何处训练、评分和编辑模型。 满意以后，即可将训练实验转换为*预测实验*（已配置为对用户数据进行评分），使模型做好部署准备。

你可以在[教程1：预测信用风险](tutorial-part1-credit-risk.md)中查看此过程的示例。

本文深入分析了将训练实验转换为预测实验并对后者进行部署的细节。 了解这些细节之后，即可学习如何配置已部署模型，使之更高效。



## <a name="overview"></a>概述 

将训练实验转换为预测实验的过程涉及三个步骤：

1. 将机器学习算法模块替换为已训练模型。
2. 仅对需要评分的模块的实验进行剪裁。 训练实验包括训练所需的大量模块，但是一旦模型经过训练，则不再需要这些模块。
3. 定义模型如何接受 Web 服务用户提供的数据，以及返回什么样的数据。

> [!TIP]
> 在训练实验中，一直关注的是如何使用自己的数据对模型继续训练和评分。 但部署以后，用户会向模型发送新的数据，而该模型则会返回预测结果。 因此，将训练实验转换为预测实验并使其可供部署以后，请关注他人会如何使用该模型。
> 
> 

## <a name="set-up-web-service-button"></a>设置 Web 服务按钮
运行实验（单击实验画布底部的“运行”）以后，请单击“设置 Web 服务”按钮（选择“预测 Web 服务”选项）。 “设置 Web 服务”执行三个步骤，将训练实验转换为预测实验：

1. 它将已训练模型保存在模块调色板的“已训练模型”部分（位于实验画布左侧）。 然后，它将机器学习算法和[训练模型][train-model]模块替换为保存的定型模型。
2. 它会分析实验，删除那些显然只用于训练且不再需要的模块。
3. 它会将 _Web 服务输入_和_输出_模块插入实验中的默认位置（这些模块接受并返回用户数据）。

例如，以下试验使用示例人口普查数据，训练双类提升的决策树模型：

![训练实验](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

在此实验的模块执行四个基本上不同的函数：

![模块函数](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

将此训练实验转换为预测实验时，不再需要这些模块中的某些模块，因此会将其丢弃，或者让它们充当其他用途：

* **数据** - 在评分过程中不使用示例数据集中的数据 - web 服务的用户将提供要评分的数据。 但是，训练模型使用此数据集的元数据（如数据类型）。 因此，需要将数据集保存在预测试验中，以便它可以提供此元数据。

* **准备** - 根据提交的、用于评分的用户数据，这些模块可能需要（也可能不需要）处理传入的数据。 “设置 Web 服务”按钮没有这些功能，需要确定如何处理它们。
  
    例如，在此示例中，示例数据集可能包含缺少的值，因此包含了一个用于处理它们的[清理缺失数据][clean-missing-data]模块。 另外，示例数据集还包括训练该模型时不需要的列。 因此，包含了 "[在数据集中选择列][select-columns]" 模块来排除数据流中的额外列。 如果你知道将通过 web 服务提交以进行评分的数据没有缺失值，则可以删除 "[清理缺失数据][clean-missing-data]" 模块。 但是，由于 "[选择数据集中的列][select-columns]" 模块有助于定义定型模型所需的数据列，因此该模块需要保留。

* **训练** - 这些模块用于模型的训练。 单击“设置 Web 服务”时，这些模块会被替换为单个模块，其中包含已训练的模型。 此新模块保存在模块调色板的“训练模型”部分。

* **分数**-在此示例中，[拆分数据][split]模块用于将数据流划分为测试数据和定型数据。 在预测实验中，我们不再定型，因此可以删除[拆分数据][split]。 同样，第二个[评分模型][score-model]模块和[评估模型][evaluate-model]模块用于比较测试数据的结果，因此预测实验不需要这些模块。 但是，需要使用剩余的[评分模型][score-model]模块，以通过 web 服务返回分数结果。

单击“设置 Web 服务”示例如下所示：

![转换预测实验](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

要准备将实验部署为 Web 服务，“设置 Web 服务”所做的工作可能已经足够了。 但是，可能想要执行特定于试验的一些附加工作。

### <a name="adjust-input-and-output-modules"></a>调整输入和输出模块
在训练实验中，使用定型数据集，并对其进行处理来获取窗体中机器学习算法所需的数据。 对于希望通过 Web 服务接收的数据，如果不需要上述处理，则可将其忽略：将“Web 服务输入模块”移动到试验中的另一个模块。 现在，用户的数据将到达此位置的模型。

例如，默认情况下“设置 Web 服务”将“Web 服务输入”模块置于数据流的顶部，如上图所示。 不过，我们可以手动对“Web 服务输入”定位，使其通过数据处理模块：

![移动 web 服务输入](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

通过 web 服务提供的输入数据现在直接传递到分数模型模块，无需任何预处理。

相同地，默认情况下“设置 Web 服务”将“Web 服务输入”模块放置在数据的流顶部。 在此示例中，web 服务会将[评分模型][score-model]模块的输出返回给用户，其中包括完整的输入数据向量以及评分结果。
但是，若要返回与众不同的内容，则可在 **Web 服务输出** 模块之前添加其他模块。 

例如，若要只返回评分结果，而不返回输入数据的整个向量，请[在数据集模块中添加 "选择列][select-columns]"，以排除除计分结果之外的所有列。 然后，将 " **Web 服务输出**" 模块移动到 "[选择数据集中的列][select-columns]" 模块的输出。 该实验如下所示：

![移动 web 服务输出](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>添加或删除其他数据处理模块
如果知道在评分过程不再需要试验中的多个模块，则可以删除这些模块。 例如，由于我们已将 " **Web 服务输入**" 模块移动到数据处理模块后的某个点，因此可以从预测实验中删除 "[清理缺失数据][clean-missing-data]" 模块。

现在，预测试验如下所示：

![删除其他模块](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>添加其他 Web 服务参数
在某些情况下，可能希望 web 服务用户在访问该服务时可以更改模块的行为。 *Web 服务参数*允许执行此操作。

常见的示例是设置[导入数据][import-data]模块，以便在访问 web 服务时，已部署的 web 服务的用户可以指定其他数据源。 或配置[导出数据][export-data]模块以指定其他目标。

可以定义 Web 服务参数并将其与一个或多个模块参数关联，可以指定它们是否是必需或可选。 访问该服务时，Web 服务的用户可为这些参数提供值，并可对模块操作进行相应的修改。

有关什么是 Web 服务参数以及如何使用它们的详细信息，请参阅[使用 Azure 机器学习 Web 服务参数][webserviceparameters]。

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>将预测试验外部署为 Web 服务
现在，预测试验已充分做好准备，可将其部署为 Azure web 服务。 使用 web 服务，用户可以将数据发送到模型，该模型将返回其预测。

有关完整的部署过程的详细信息，请参阅[部署 Azure 机器学习 web 服务][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
