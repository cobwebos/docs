---
title: 模型成为 web 服务的方式
titleSuffix: ML Studio (classic) - Azure
description: 概述 Azure 机器学习 Studio （经典）模型如何从开发实验到 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217919"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>机器学习 Studio （经典）模型如何从实验到 Web 服务的进展

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure 机器学习 Studio （经典）提供了一个交互式画布，可用于开发、运行、测试和迭代表示预测分析模型的***实验***。 有大量各种不同的模块可用于：

* 将数据输入到实验
* 操作该数据
* 使用机器学习算法训练模型
* 为模型评分
* 评估结果
* 输出最终值

如果你对实验感到满意，则可以将其部署为***经典 Azure 机器学习 web 服务***或新的***Azure 机器学习 web 服务***，以便用户能够向其发送新数据并接收回结果。

在本文中，我们提供了有关机器学习模型如何从开发实验逐步进展为运营 Web 服务的机制概述。

> [!NOTE]
> 还有其他方法可以开发和部署机器学习模型，但本文重点介绍如何使用机器学习 Studio （经典）。 例如，若要阅读如何使用 R 创建经典预测 Web 服务的说明，请参阅博客文章[使用 RStudio 和 Azure 机器学习工作室生成和部署预测 Web 应用](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)。
>
>

尽管 Azure 机器学习 Studio （经典）旨在帮助你开发和部署*预测分析模型*，但可以使用 Studio （经典）来开发不包含预测分析模型的实验。 例如，实验可能只是输入数据，对其进行操作，并输出结果。 就像预测分析实验一样，你可以将此非预测试验部署为 Web 服务，但这是一个更简单的过程，因为试验不会对机器学习模型进行定型或评分。 虽然这并不是以这种方式使用 Studio （经典）的典型方法，但我们会将其包含在讨论中，以便我们可以提供 Studio （经典）工作原理的完整说明。

## <a name="developing-and-deploying-a-predictive-web-service"></a>开发和部署预测性 Web 服务
下面是使用机器学习 Studio （经典）开发和部署典型解决方案时遵循的步骤：

![部署流](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*图 1 - 典型预测分析模型的各个阶段*

### <a name="the-training-experiment"></a>训练实验
***训练试验***是在机器学习 Studio （经典）中开发 Web 服务的初始阶段。 训练实验的目的是提供一个开发、测试、循环访问和最终定型机器学习模型的环境。 即使您寻找最佳解决方案，您甚至可以同时训练多个模型，但完成试验后，您将选择单个训练的模型并消除实验中的其余部分。 有关开发预测分析实验的示例，请参阅[Azure 机器学习 Studio （经典）中的为信用风险评估开发预测分析解决方案](tutorial-part1-credit-risk.md)。

### <a name="the-predictive-experiment"></a>预测性实验
在训练实验中具有定型模型后，单击 "**设置 Web 服务**"，并选择 "机器学习 Studio （经典）中的**预测性 Web 服务**"，以启动将训练实验转换为***预测实验***的过程。 预测性实验旨在使用定型模型对新数据进行评分，目的是为了最终变得如 Azure Web 服务一样具备可操作性。

会通过以下步骤完成该转换：

* 将用于训练的模块集转换为单个模块，并将其另存为定型模型
* 去除任何与评分不相关的多余模块
* 添加最终 Web 服务将使用的输入和输出端口

可能你会有想要执行的其他更改，以使预测试验准备好部署为 Web 服务。 例如，如果想要 Web 服务仅输出结果的一部分，可以在输出端口前添加筛选模块。

在此转换过程中，不会放弃训练实验。 完成此过程后，Studio （经典）中将有两个选项卡：一个用于定型试验，另一个用于预测试验。 通过此方法，在部署 Web 服务之前，可以更改训练实验，并重新生成预测实验。 也可以保存一份训练实验副本，以开始另一行的实验。

> [!NOTE]
> 单击“**预测 Web 服务**”时，会启动将训练实验转换为预测实验的自动进程，并且这在大多数情况下可正常运行。 如果训练实验过于复杂（例如，有多种联合使用的训练途径），可能需要手动执行此转换。 有关详细信息，请参阅[如何在 Azure 机器学习 Studio （经典）中准备要部署的模型](convert-training-experiment-to-scoring-experiment.md)。
>
>

### <a name="the-web-service"></a>Web 服务
一旦你认为你的预测实验已准备就绪，你可以将你的服务作为经典 Web 服务或基于 Azure 资源管理器的新 Web 服务部署。 要通过将其部署为*经典机器学习 Web 服务*来实施模型，请单击“**部署 Web 服务**”，然后选择“**部署 Web 服务[经典]** ”。 要作为*新的机器学习 Web 服务*进行部署，请单击“**部署 Web 服务**”，并选择“**部署 Web 服务[新]** ”。 用户现在可以使用 Web 服务 REST API 将数据发送到模型并接收返回的结果。 有关详细信息，请参阅[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>非典型情况：创建一个非预测性的 Web 服务
如果实验未定型预测分析模型，则无需创建定型实验和评分实验，只需进行一次试验，即可将其部署为 Web 服务。 机器学习 Studio （经典）通过分析你使用的模块来检测实验是否包含预测模型。

循环访问实验并满足此要求后：

1. 单击“**设置 Web 服务**”，然后选择“**重新训练 Web 服务**” - 会自动添加输入和输出节点
2. 单击“**运行**”
3. 单击“**部署 Web 服务**”，并选择“**部署 Web 服务[经典]** ”或“**部署 Web 服务[新]** ”，具体取决于要部署的环境。

Web 服务现已部署，并且可以像预测的 Web 服务一样对其进行访问和管理。

## <a name="updating-your-web-service"></a>更新 web 服务
现在，你已将实验部署为 Web 服务，如果需要更新它，该怎么办？

这取决于需要更新的内容：

**是要更改输入或输出，还是要修改 Web 服务操作数据的方式**

如果您不更改该模型，而只是更改 Web 服务处理数据的方式，则可以编辑预测试验，然后单击 "**部署 Web 服务**"，然后选择 "部署 web 服务 **[经典]** " 或 "**部署 web 服务 [新]** "。 Web 服务将停止，会对更新的预测实验进行部署，并重新启动 Web 服务。

下面是一个示例：假设预测实验返回输入数据的整个行和预测结果。 可能决定想要 Web 服务只返回结果。 那么，可以在预测实验中添加**项目列**，紧接在输出端口前，以排除除了结果之外的列。 单击“**部署 Web 服务**”时，并再次选择“**部署 Web 服务[经典]** ”或“**部署 Web 服务[新]** ”，Web 服务将更新。

**想要使用新数据重新训练模型**

如果要保留机器学习模型，但希望使用新数据对其进行重新训练，有两个选择：

1. **Web 服务运行时重新训练模型** - 如果要在预测 Web 服务正在运行时重新训练模型，可以通过对训练实验进行部分修改，以使其成为***重新训练实验***来完成此操作，然后可以将其部署为 ***重新训练 Web* 服务**。 有关如何执行此操作的说明，请参阅[以编程方式重新训练机器学习模型](/azure/machine-learning/studio/retrain-machine-learning-model)。
2. **返回原始训练实验并使用不同的训练数据来开发模型** - 预测实验链接到 Web 服务，但训练实验不是以此方式直接链接。 如果修改原始训练实验并单击“**设置 Web 服务**”，它将创建一个*新的*预测实验，在其部署时会创建一项*新的* Web 服务。 它不只是更新原始 Web 服务。

   如果需要修改训练实验，请打开它并单击“**另存为**”以制作副本。 这会使原始训练实验、预测实验和 Web 服务保持不变。 现在可以使用更改来创建新的 Web 服务。 部署新的 Web 服务后，你可以决定是停止以前的 Web 服务还是使其与新服务一起运行。

**想要训练不同的模型**

如果想更改原始预测实验，如选择不同的计算机学习算法、尝试不同的训练方法等，则需要按照上述重新训练模型的第二个过程：打开培训实验，单击“**另存为**”来创建一个副本，并在新路径下开始开发模型、创建预测实验和部署 Web 服务。 这会创建一个与原始 Web 服务无关的新的 Web 服务 - 可以决定运行其中一个，或两个都保持运行。

## <a name="next-steps"></a>后续步骤
有关开发和实验过程的详细信息，请参阅以下文章：

* 转换试验-[如何准备模型以便在 Azure 机器学习 Studio 中部署（经典）](convert-training-experiment-to-scoring-experiment.md)
* 部署 Web 服务 - [部署 Azure 机器学习 Web 服务](deploy-a-machine-learning-web-service.md)
* 重新训练模型 - [以编程方式重新训练机器学习模型](/azure/machine-learning/studio/retrain-machine-learning-model)

有关整个过程的示例，请参阅：

* [机器学习教程：在 Azure 机器学习 Studio （经典）中创建第一个试验](create-experiment.md)
* [演练：在 Azure 机器学习中为信用风险评估开发预测分析解决方案](tutorial-part1-credit-risk.md)

