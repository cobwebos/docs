---
title: 教程 3：部署信用风险模型
titleSuffix: Azure Machine Learning Studio
description: 本详细教程介绍如何创建预测分析解决方案，用于在 Azure 机器学习工作室中进行信用风险评估。 本教程是由三个部分构成的系列教程的第三部分。 其中介绍了如何将模型部署为 Web 服务。
keywords: 信用风险, 预测分析解决方案, 风险评估, 部署, web 服务
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6e3f0613e89ef7d6e9b44e519226a793940d41f6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56006295"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio"></a>教程 3：部署信用风险模型 - Azure 机器学习工作室

在本教程中，我们将深入探讨开发预测分析解决方案的过程。 我们将在机器学习工作室中开发一个简单模型。  然后将该模型部署为 Azure 机器学习 Web 服务。  部署的模型将使用新数据进行预测。 本教程是**由三个部分构成的系列教程的第三部分**。

假设用户需要根据他们提供的贷款申请相关信息预测个人的信用风险。  

信用风险评估是个较为复杂的问题，但本教程会将其适当简化。 我们将使用它作为示例，展示如何使用 Microsoft Azure 机器学习来创建预测分析解决方案。 对此解决方案，我们将使用 Azure 机器学习工作室和机器学习 Web 服务。 

在这篇由三个部分构成的教程中，我们将从公开的信用风险数据着手。  然后开发并训练预测模型。  最后将该模型部署为 Web 服务。

在[本教程的第一部分](tutorial-part1-credit-risk.md)中，我们已创建了一个机器学习工作室工作区、上传了数据，并创建了试验。

在[本教程的第二部分](tutorial-part2-credit-risk-train.md)，我们已训练并评估模型。

本教程部分介绍以下操作：

> [!div class="checklist"]
> * 准备部署
> * 部署 Web 服务
> * 测试 Web 服务
> * 管理 Web 服务
> * 访问 Web 服务

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>先决条件

完成[本教程的第二部分](tutorial-part2-credit-risk-train.md)。

## <a name="prepare-for-deployment"></a>准备部署
为了让其他人可以使用本教程中开发的预测模型，可将它部署为 Azure 上的 Web 服务。

截至目前，我们一直在试验如何训练模型。 但是已部署的服务不再进行训练，它会根据模型，通过对用户的输入进行评分来生成新的预测。 因此我们需要做好一些准备工作，将此试验从***训练***试验转换为***预测***试验。 

部署准备过程由三个步骤构成：  

1. 删除一个模型
1. 将创建的训练试验转换为预测试验
1. 将预测试验外部署为 Web 服务

### <a name="remove-one-of-the-models"></a>删除一个模型

首先，需要稍微修整一下此试验。 目前试验中有两个不同的模型，但将试验部署为 Web 服务时，只需要一个模型。  

假设已确定提升树模型的性能强于 SVM 模型。 因此，首先是删除[双类支持向量机][two-class-support-vector-machine]模块，以及用于训练该模块的其他模块。 可能需要单击试验画布底部的“另存为”，先创建一个试验副本。

需要删除以下模块：  

* [双类支持向量机][two-class-support-vector-machine]
* 与它连接的[训练模型][train-model]和[评分模型][score-model]模块
* [规范化数据][normalize-data]（两者）
* [评估模型][evaluate-model]（因为我们已评估完模型）

选择每个模块并按 Delete 键，或右键单击模块并选择“删除”。 

![删除 SVM 模型](./media/tutorial-part3-credit-risk-deploy/publish3a.png)


模型现在看起来应与下图类似：

![删除 SVM 模型](./media/tutorial-part3-credit-risk-deploy/publish3.png)

现已准备好使用[双类提升决策树][two-class-boosted-decision-tree]部署此模型。

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>将训练实验转换为预测实验

要使此模型准备好进行部署，需要将此训练试验转换为预测试验。 这涉及到三个步骤：

1. 保存已训练的模型，并替换训练模块
1. 修整试验，删除只有训练需要的模块
1. 定义 Web 服务接受输入的位置以及生成输出的位置

可以手动执行此操作，不过幸好上述三个步骤只需单击试验画布底部的“设置 Web 服务”（以及选择“预测 Web 服务”选项）即可完成。

> [!TIP]
> 要更详细地了解在将训练实验转换为预测实验时会发生什么情况，请参阅[如何准备模型以便在 Azure 机器学习工作室中进行部署](convert-training-experiment-to-scoring-experiment.md)。

单击“设置 Web 服务”时，会发生以下情况：

* 训练的模型已转换为单个“已训练模型”模块并存储在实验画布左侧的模块控制板中（可以在“已训练模型”下找到此模块）
* 用于训练的模块被删除，具体包括：
  * [双类提升决策树][two-class-boosted-decision-tree]
  * [训练模型][train-model]
  * [拆分数据][split]
  * 用于测试数据的第二个[执行 R 脚本][execute-r-script]模块
* 保存的训练模型已添加回实验中
* “Web 服务输入”和“Web 服务输出”模块已添加（这些模块标识用户的数据会在何处进入模型、返回什么数据、何时访问 Web 服务）

> [!NOTE]
> 可以看到，实验分两部分保存在多个选项卡下，这些选项卡已添加到实验画布顶部。 原始训练实验位于“训练实验”选项卡下，新创建的预测实验位于“预测实验”下。 将预测试验是将要部署为 Web 服务的试验。

需要对此特定试验执行一个额外的步骤。
已添加两个[执行 R 脚本][execute-r-script]模块来向数据提供加权函数。 该函数仅用于训练和测试，因此可以在最终模型中移除这些模块。
机器学习工作室已在删除[拆分][split]模块时删除了一个[执行 R 脚本][execute-r-script]模块。 现在可以删除另一个模块，并直接将[元数据编辑器][metadata-editor]连接到[评分模型][score-model]。    

试验现在应如下所示：  

![为训练的模型评分](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> 你可能会疑惑我们为何要在预测试验中留下“UCI 德国信用卡数据”数据集。 服务要评分的是用户数据，而不是原始数据集，为何要让原始数据集保留在模型中？
> 
> 服务的确无需原始信用卡数据， 但需要该数据的架构，例如，有多少个列及哪些列是数值等信息。 需要此架构信息才能解释用户的数据。 保留连接这些组件的目的是在运行服务时，评分模型可获得数据集架构。 使用的不是数据，而只是架构。  
> 
>需要注意的一件重要事情是，如果原始数据集包含标签，那么Web 输入中的所需架构也会需要带标签的列！ 解决此问题的一种方法是在将 Web 输入和训练数据集连接到通用模块之前，删除标签以及在训练数据集中但不在 Web 输入中的任何其他数据。 
> 

最后一次运行试验（单击“运行”）。要验证模型是否仍然正常工作，请单击[评分模型][score-model]模块的输出，并选择“查看结果”。 此时将显示原始数据，以及信用风险值（“评分标签”）和评分概率值（“评分概率”）。 

## <a name="deploy-the-web-service"></a>部署 Web 服务
可以将实验部署为经典 Web 服务或基于 Azure 资源管理器的新 Web 服务。

### <a name="deploy-as-a-classic-web-service"></a>部署为经典 Web 服务
要部署派生自试验的经典 Web 服务，请单击画布下面的“部署 Web 服务”，并选择“部署 Web 服务 [经典]”。 机器学习工作室会将试验部署为 Web 服务，并你将转到该 Web 服务的仪表板。 在此页中可以返回试验（“查看快照”或“查看最新”），以及运行简单的 Web 服务测试（请参阅下面的**测试 Web 服务**）。 此处，还提供了有关创建可访问 Web 服务的应用程序的信息（本教程的下一步骤提供了更多相关信息）。

![Web 服务仪表板](./media/tutorial-part3-credit-risk-deploy/publish6.png)


可以单击“配置”选项卡来配置服务。可以在此处修改服务名称（默认指定试验名称）并输入说明。 此外，可为输入和输出数据指定更友好的标签。  

![配置 Web 服务](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>部署为新 Web 服务

> [!NOTE] 
> 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](manage-new-webservice.md)。 

若要部署从实验派生的新的 Web 服务，请执行以下操作：

1. 单击画布下面的“部署 Web 服务”，并选择“部署 Web 服务 [新]”。 机器学习工作室会你将转到 Azure 机器学习 Web 服务的“部署实验”页。

1. 输入 Web 服务的名称。 

1. 对于“价格计划”，可以选择现有的定价计划，也可以选择“新建”，并为新计划提供一个名称并选择每月计划选项。 计划层默认为默认区域的计划，并且 Web 服务将部署到该区域。

1. 单击“部署”。

数分钟后，会打开 Web 服务的“快速入门”页。

可以单击“配置”选项卡来配置服务。可以在此处修改服务标题并为其提供说明。 

若要测试 Web 服务，请单击“测试”选项卡（请参阅下面的**测试 Web 服务**）。 有关创建可访问 Web 服务的应用程序的信息，请单击“使用”选项卡（本教程的下一步骤会提供更多详细信息）。

> [!TIP]
> 部署 Web 服务后可以更新它。 例如，如果想要更改模型，请编辑训练实验、调整模型参数，并单击“部署 Web 服务”，选择“部署 Web 服务 [经典]”或“部署 Web 服务 [新]”。 重新部署试验时，该试验将替换 Web 服务，然后，使用的就是已更新的模型了。  
> 
> 

## <a name="test-the-web-service"></a>测试 Web 服务

访问 Web 服务时，用户的数据将通过“Web 服务输入”模块传递给[评分模型][score-model]模块并评分。 根据设置预测试验的方式，模型预期采用的数据格式与原始信用风险数据集的格式相同。
然后，结果将通过“Web 服务输出”模块从 Web 服务返回给用户。

> [!TIP]
> 根据预测试验的配置方式，将返回[评分模型][score-model]模块的整个结果。 这包括所有输入数据以及信用风险值和评分概率。 如果希望返回不同的结果 - 例如，只返回信用风险值， 可以在[评分模型][score-model]和 **Web 服务输出**之间插入[投影列][project-columns]模块，用于排除不想要让 Web 服务返回的列。 
> 
> 

可以在**机器学习工作室**或 **Azure 机器学习 Web 服务**门户中测试经典 Web 服务。
只能在 **Azure 机器学习 Web 服务**门户中测试新的 Web 服务。

> [!TIP]
> 在 Azure 机器学习 Web 服务门户中测试时，可以让门户创建用于测试请求-响应服务的示例数据。 在“配置”页上，针对“启用示例数据?”选择“是”。 打开“测试”页上的“请求-响应”选项卡时，门户会在其中填充取自原始信用风险数据集的示例数据。

### <a name="test-a-classic-web-service"></a>测试经典 Web 服务

可以在机器学习工作室或机器学习 Web 服务门户中测试经典 Web 服务。 

#### <a name="test-in-machine-learning-studio"></a>在机器学习工作室中测试

1. 在 Web 服务的“仪表板”页上，单击“默认终结点”下的“测试”按钮。 将弹出一个对话框，要求用户输入服务的输入数据。 这些是在原始信用风险数据集中出现的列。  

1. 输入一组数据，并单击“确定”。 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>在机器学习 Web 服务门户中测试

1. 在 Web 服务的“仪表板”页上，单击“默认终结点”下的“测试预览”链接。 Azure 机器学习 Web 服务门户中的 Web 服务终结点测试页将打开，并要求提供服务的输入数据。 这些是在原始信用风险数据集中出现的列。

2. 单击“测试请求-响应”。 

### <a name="test-a-new-web-service"></a>测试新的 Web 服务

只能在 Azure 机器学习 Web 服务门户中测试新的 Web 服务。

1. 在 [Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户中，单击页面顶部的“测试”。 此时会打开“测试”页，可在其中输入服务的数据。 显示的输入字段对应于原始信用风险数据集中出现的列。 

1. 输入一组数据，并单击“测试请求-响应”。

测试结果会显示在页面右侧的输出列中。 


## <a name="manage-the-web-service"></a>管理 Web 服务

部署 Web 服务（经典或新式）后，可以从 [Microsoft Azure 机器学习 Web 服务门户](https://services.azureml.net/quickstart)管理它。

监视 Web 服务的性能：

1. 登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户
1. 单击“Web 服务”
1. 单击 Web 服务
1. 单击“仪表板”

## <a name="access-the-web-service"></a>访问 Web 服务

本教程的上一步骤已部署了使用信用风险预测模型的 Web 服务。 现在用户能够向其发送数据并收到结果。 

Web 服务是一种 Azure Web 服务，可以两种方式之一使用 REST API 接收和返回数据：  

* **请求/响应** - 用户通过使用 HTTP 协议，向服务发送信用数据的一个或多个行，而服务会使用一个或多个结果集进行响应。
* **Batch 执行** - 用户将信用数据的一个或多个行存储在 Azure blob 中，然后将 blob 位置发送到服务。 服务将对输入 blob 中数据的所有行进行评分，将结果存储到另一 blob 中，并返回该容器的 URL。  

访问经典 Web 服务最快且最简单的方法是通过 [Azure ML 请求-响应服务 Web 应用](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)或 [Azure ML 批处理执行服务 Web 应用模板](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)。

这些 Web 应用模板可生成自定义 Web 应用，该应用了解 Web 服务的输入数据及返回结果。 所需操作就是提供 Web 服务和数据的访问权限，余下操作由模板完成。

有关使用 Web 应用模板的详细信息，请参阅[使用 Azure 机器学习 Web 服务与 Web 应用模板](consume-web-service-with-web-app-template.md)。



## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，我们已完成以下步骤：

> [!div class="checklist"]
> * 准备部署
> * 部署 Web 服务
> * 测试 Web 服务
> * 管理 Web 服务
> * 访问 Web 服务

还可使用 R、C# 和 Python 编程语言中提供的起始代码开发自定义应用程序，以访问 Web 服务。

> [!div class="nextstepaction"]
> [使用 Azure 机器学习 Web 服务](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/