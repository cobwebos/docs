---
title: "如何将 Azure 机器学习模型变为 Web 服务 | Microsoft 文档"
description: "Azure 机器学习模型如何从开发实验逐步进展为运营 Web 服务的机制概述。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 209d1cbe4f1ad08a98caf0a8d16f5b0ee45468ca
ms.lasthandoff: 03/31/2017


---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>机器学习模型如何从实验逐步变为运营 Web 服务
Azure 机器学习工作室提供交互式画布，允许你开发、运行、测试和迭代表示预测分析模型的***实验***。 有大量各种不同的模块可用于：

* 将数据输入到实验
* 操作该数据
* 使用机器学习算法训练模型
* 为模型评分
* 评估结果
* 输出最终值

一旦你对实验感到满意，则可以将其部署为***经典 Azure 机器学习 Web 服务***或***新的 Azure 机器学习 Web 服务***，以便用户可以向其发送新的数据，并接收返回的结果。

在本文中，我们提供了有关机器学习模型如何从开发实验逐步进展为运营 Web 服务的机制概述。

> [!NOTE]
> 还有其他方法可用于开发和部署机器学习模型，但本文的重点是如何使用机器学习工作室。 例如，若要阅读如何使用 R 创建经典预测 Web 服务的说明，请参阅博客文章 [Build & Deploy Predictive Web Apps Using RStudio and Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)（使用 RStudio 和 Azure ML 生成和部署预测 Web 应用）。
> 
> 

虽然 Azure 机器学习工作室主要是为了帮助你开发和部署*预测分析模型*，但也可以使用工作室来开发不包括预测分析模型的实验。 例如，实验可能只是输入数据，对其进行操作，然后输出结果。 就像预测分析实验一样，你可以将此非预测实验部署为 Web 服务，但它是一个更简单的过程，因为实验不会对机器学习模型进行训练或评分。 尽管这不是通常使用工作室的方法，但我们会将其包括在稍后的讨论中，以便可以提供有关工作室工作原理的完整说明。

## <a name="developing-and-deploying-a-predictive-web-service"></a>开发和部署预测性 Web 服务
以下是在你使用机器学习工作室进行开发和部署时典型解决方案所遵循的各个阶段：

![部署流](media/machine-learning-model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*图 1 - 典型预测分析模型的各个阶段*

### <a name="the-training-experiment"></a>训练实验
***训练实验***是在机器学习工作室中开发 Web 服务的初始阶段。 训练实验的目的是为你提供一个开发、测试、循环访问和最终定型机器学习模型的环境。 在寻找最佳解决方案时，甚至可以同时训练多个模型，但完成实验后，将选择一个定型模型，并去除实验中的其余部分。 有关开发预测分析实验的示例，请参阅 [在 Azure 机器学习中为信用风险评估开发预测分析解决方案](machine-learning-walkthrough-develop-predictive-solution.md)。

### <a name="the-predictive-experiment"></a>预测性实验
在训练实验中具有定型模型后，在机器学习工作室中单击“**设置 Web 服务**”，然后选择“**预测性 Web 服务**”，以启动将训练实验转换为***预测性实验***的过程。 预测性实验旨在使用定型模型对新数据进行评分，目的是为了最终变得如 Azure Web 服务一样具备可操作性。

会通过以下步骤完成该转换：

* 将用于训练的模块集转换为单个模块，并将其另存为定型模型
* 去除任何与评分不相关的多余模块
* 添加最终 Web 服务将使用的输入和输出端口

可能你会有想要执行的其他更改，以使预测实验准备好部署为 Web 服务。 例如，如果想要 Web 服务仅输出结果的一部分，可以在输出端口前添加筛选模块。

在此转换过程中，不会放弃训练实验。 该过程完成后，工作室中将有两个选项卡：一个用于训练实验，一个用于预测实验。 通过此方法，在部署 Web 服务之前，可以更改训练实验，并重新生成预测实验。 也可以保存一份训练实验副本，以开始另一行的实验。

> [!NOTE]
> 单击“**预测 Web 服务**”时，会启动将训练实验转换为预测实验的自动进程，并且这在大多数情况下可正常运行。 如果训练实验过于复杂（例如，有多种联合使用的训练途径），你可能需要手动执行此转换。 有关详细信息，请参阅[如何准备模型以便在 Azure 机器学习工作室中进行部署](machine-learning-convert-training-experiment-to-scoring-experiment.md)。
> 
> 

### <a name="the-web-service"></a>Web 服务
预测实验准备就绪让你感到满意后，即可基于 Azure Resource Manager 将服务部署为经典 Web 服务或新的 Web 服务。 若要通过将其部署为*经典机器学习 Web 服务*来实施模型，请单击“**部署 Web 服务**”，然后选择“**部署 Web 服务[经典]**”。 若要作为*新的机器学习 Web 服务*进行部署，请单击“**部署 Web 服务**”，然后选择“**部署 Web 服务[新]**”。 用户现在可以使用 Web 服务 REST API 将数据发送到你的模型并接收返回的结果。 有关详细信息，请参阅[如何使用已从机器学习实验部署的 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>非典型情况：创建一个非预测性的 Web 服务
如果实验没有对预测分析模型进行训练，则无需创建训练实验和评分实验 - 只有一个实验，并且可以将其部署为 Web 服务。 机器学习工作室可通过分析所使用的模块，检测你的实验是否包含预测性模型。

在迭代实验并感到满意后：

1. 单击“**设置 Web 服务**”，然后选择“**重新训练 Web 服务**” - 将自动添加输入和输出节点
2. 单击“**运行**”
3. 单击“**部署 Web 服务**”，然后选择“**部署 Web 服务[经典]**”或“**部署 Web 服务[新]**”，具体取决于要部署的环境。

Web 服务现已部署，并且可以像预测的 Web 服务一样对其进行访问和管理。

## <a name="updating-your-web-service"></a>更新 Web 服务
至此，已经将实验部署为 Web 服务，如果需要更新它呢？

这取决于需要更新的内容：

**是要更改输入或输出，还是要修改 Web 服务操作数据的方式**

如果不更改该模型，而只是更改 Web 服务处理数据的方式，则可以编辑预测实验，然后单击“**部署 Web 服务**”，再次选择“**部署 Web 服务[经典]**”或“**部署 Web 服务[新]**”。 Web 服务将停止，会对更新的预测实验进行部署，并重新启动 Web 服务。

以下是一个示例：假设预测实验返回输入数据的整个行与预测结果。 你可能决定想要 Web 服务只返回结果。 那么，你可以在预测实验中添加**项目列**，紧接在输出端口前，以排除除了结果之外的列。 单击“**部署 Web 服务**”时，并再次选择“**部署 Web 服务[经典]**”或“**部署 Web 服务[新]**”，Web 服务将更新。

**想要使用新数据重新训练模型**

如果要保留机器学习模型，但希望使用新数据对其进行重新训练，你有两个选择：

1. **Web 服务运行时重新训练模型** - 如果要在预测 Web 服务正在运行时重新训练模型，可以通过对训练实验进行部分修改，以使其成为***重新训练实验***来完成此操作，然后可以将其部署为***重新训练 Web* 服务**。 有关如何执行此操作的说明，请参阅[以编程方式重新训练机器学习模型](machine-learning-retrain-models-programmatically.md)。
2. **返回原始训练实验并使用不同的训练数据来开发模型** - 预测实验链接到 Web 服务，但训练实验不是以此方式直接链接。 如果修改原始训练实验并单击“**设置 Web 服务**”，它将创建一个*新的*预测实验，在其部署时将创建一项*新的* Web 服务。 它不只是更新原始 Web 服务。
   
   如果需要修改训练实验，请打开它并单击“**另存为**”以制作副本。 这将使原始训练实验、预测实验和 Web 服务保持不变。 现在可以使用你的更改来创建新的 Web 服务。 部署了新的 Web 服务后，可以决定是否要停止以前的 Web 服务，或使其与新的服务一起运行。

**想要训练不同的模型**

如果想更改原始预测实验，如选择不同的计算机学习算法、尝试不同的训练方法等，则需要按照上述重新训练模型的第二个过程：打开培训实验，单击“**另存为**”来创建一个副本，然后在新路径下开始开发模型、创建预测实验和部署 Web 服务。 这将创建一个与原始 Web 服务无关的新的 Web 服务 - 你可以决定运行其中一个，或两个都保持运行。

## <a name="next-steps"></a>后续步骤
有关开发和实验过程的详细信息，请参阅以下文章：

* 转换实验 - [如何准备模型以便在 Azure 机器学习工作室中进行部署](machine-learning-convert-training-experiment-to-scoring-experiment.md)
* 部署 Web 服务 - [部署 Azure 机器学习 Web 服务](machine-learning-publish-a-machine-learning-web-service.md)
* 重新训练模型 - [以编程方式重新训练机器学习模型](machine-learning-retrain-models-programmatically.md)

有关整个过程的示例，请参阅：

* [机器学习教程：在 Azure 机器学习工作室中创建第一个实验](machine-learning-create-experiment.md)
* [演练：在 Azure 机器学习中为信用风险评估开发预测分析解决方案](machine-learning-walkthrough-develop-predictive-solution.md)


