---
title: "步骤 5：部署机器学习 Web 服务 | Microsoft Docs"
description: "开发预测解决方案演练的步骤 5：在 Azure 机器学习工作室中部署一个 Web 服务形式的预测试验。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: cec1bcceea158a20742c7019a266dcefaac4c9cf
ms.lasthandoff: 03/31/2017


---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>演练步骤 5：部署 Azure 机器学习 Web 服务
这是演练[在 Azure 机器学习中开发预测分析解决方案](machine-learning-walkthrough-develop-predictive-solution.md)的第五个步骤。

1. [创建机器学习工作区](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [上载现有数据](machine-learning-walkthrough-2-upload-data.md)
3. [创建新试验](machine-learning-walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. **部署 Web 服务**
6. [访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)

- - -
为了让其他人可以使用此演练中开发的预测模型，我们可以将它部署为 Azure 上的 Web 服务。

截至目前，我们一直在试验如何训练模型。 但是已部署的服务不再进行训练，它会根据模型，通过对用户的输入进行评分来生成新的预测。 因此我们需要做好一些准备工作，将此试验从***训练***试验转换为***预测***试验。 

此过程由三个步骤组成：  

1. 删除一个模型
2. 将创建的*训练试验*转换为*预测试验*
3. 将预测试验外部署为 Web 服务

## <a name="remove-one-of-the-models"></a>删除一个模型

首先，需要稍微修整一下此试验。 目前试验中有两个不同的模型，但将试验部署为 Web 服务时，只需要一个模型。  

假设已确定提升树模型的性能强于 SVM 模型。 因此，首先是删除[双类支持向量机][two-class-support-vector-machine]模块，以及用于训练该模块的其他模块。 可能需要单击试验画布底部的“另存为”，先创建一个试验副本。

需要删除以下模块：  

* [双类支持向量机][two-class-support-vector-machine]
* 与它连接的[训练模型][train-model]和[评分模型][score-model]模块
* [规范化数据][normalize-data]（两者）
* [评估模型][evaluate-model]（因为我们已评估完模型）

选择每个模块并按 Delete 键，或右键单击模块并选择“删除”。 

![删除 SVM 模型][3a]

模型现在看起来应与下图类似：

![删除 SVM 模型][3]

现已准备好使用[双类提升决策树][two-class-boosted-decision-tree]部署此模型。

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>将训练实验转换为预测实验

若要使此模型准备好进行部署，需要将此训练实验转换为预测实验。 这涉及到三个步骤：

1. 保存已训练的模型，然后替换训练模块
2. 修整试验，删除只有训练需要的模块
3. 定义 Web 服务接受输入的位置以及生成输出的位置

我们可以手动执行此操作，不过幸好上述三个步骤只需单击实验画布底部的“设置 Web 服务”（以及选择“预测 Web 服务”选项）即可完成。

> [!TIP]
> 若要更详细地了解在将训练实验转换为预测实验时会发生什么情况，请参阅[如何准备模型以便在 Azure 机器学习工作室中进行部署](machine-learning-convert-training-experiment-to-scoring-experiment.md)。

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
> 可以看到，实验分两部分保存在多个选项卡下，这些选项卡已添加到实验画布顶部。 原始训练实验位于“训练实验”选项卡下，新创建的预测实验位于“预测实验”下。 将预测实验是将要部署为 Web 服务的实验。

需要对此特定试验执行一个额外的步骤。
已添加两个[执行 R 脚本][execute-r-script]模块来向数据提供加权函数。 该函数仅用于训练和测试，因此可以在最终模型中移除这些模块。
机器学习工作室已在删除[拆分][split]模块时删除了一个[执行 R 脚本][execute-r-script]模块。 现在可以删除另一个模块，并直接将[元数据编辑器][metadata-editor]连接到[评分模型][score-model]。    

试验现在应如下所示：  

![为训练的模型评分][4]  

> [!NOTE]
> 你可能会惊讶我们为何要在预测试验中留下“UCI 德国信用卡数据”数据集。 服务要评分的是用户数据，而不是原始数据集，为何要让原始数据集保留在模型中？
> 
> 服务的确无需原始信用卡数据， 但需要该数据的架构，例如，有多少个列及哪些列是数值等信息。 需要此架构信息才能解释用户的数据。 保留连接这些组件的目的是在运行服务时，评分模型可获得数据集架构。 使用的不是数据，而只是架构。  
> 
> 

最后一次运行试验（单击“运行”）。若要验证模型是否仍然正常工作，请单击[评分模型][score-model]模块的输出，然后选择“查看结果”。 此时将显示原始数据，以及信用风险值（“评分标签”）和评分概率值（“评分概率”）。 

## <a name="deploy-the-web-service"></a>部署 Web 服务
可以将实验部署为经典 Web 服务或基于 Azure Resource Manager 的新 Web 服务。

### <a name="deploy-as-a-classic-web-service"></a>部署为经典 Web 服务
若要部署派生自试验的经典 Web 服务，请单击画布下面的“部署 Web 服务”，然后选择“部署 Web 服务 [经典]”。 机器学习工作室会将试验部署为 Web 服务，并将你转到该 Web 服务的仪表板。 在此页中可以返回试验（“查看快照”或“查看最新”），以及运行简单的 Web 服务测试（请参阅下面的**测试 Web 服务**）。 此处，还提供了有关创建可访问 Web 服务的应用程序的信息（本演练的下一步骤提供了更多相关信息）。

![Web 服务仪表板][6]

可以单击“配置”选项卡来配置服务。 可以在此处修改服务名称（默认指定试验名称）并输入说明。 此外，可为输入和输出数据指定更友好的标签。  

![配置 Web 服务][5]  

### <a name="deploy-as-a-new-web-service"></a>部署为新 Web 服务

> [!NOTE] 
> 若要部署新的 Web 服务，必须对要部署 Web 服务的订阅拥有充分的权限。 有关详细信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](machine-learning-manage-new-webservice.md)。 

若要部署从实验派生的新的 Web 服务，请执行以下操作：

1. 单击画布下面的“部署 Web 服务”，然后选择“部署 Web 服务 [新]”。 机器学习工作室会将你转到 Azure 机器学习 Web 服务的“部署实验”页。

2. 输入 Web 服务的名称。 

3. 对于“价格计划”，可以选择现有的定价计划，也可以选择“新建”，然后为新计划提供一个名称并选择每月计划选项。 计划层默认为默认区域的计划，并且 Web 服务将部署到该区域。

4. 单击“部署”。

数分钟后，将会打开 Web 服务的“快速入门”页。

可以单击“配置”选项卡来配置服务。 可以在此处修改服务标题并为其提供说明。 

若要测试 Web 服务，请单击“测试”选项卡（请参阅下面的**测试 Web 服务**）。 有关创建可访问 Web 服务的应用程序的信息，请单击“使用”选项卡（本演练的下一步骤会提供更多详细信息）。

> [!TIP]
> 部署 Web 服务后可以更新它。 例如，如果想要更改模型，请编辑训练实验、调整模型参数，然后单击“部署 Web 服务”，选择“部署 Web 服务 [经典]”或“部署 Web 服务 [新]”。 重新部署试验时，该试验将替换 Web 服务，然后，使用的就是已更新的模型了。  
> 
> 

## <a name="test-the-web-service"></a>测试 Web 服务

访问 Web 服务时，用户的数据将通过“Web 服务输入”模块传递给[评分模型][score-model]模块并评分。 根据我们设置预测实验的方式，模型预期采用的数据格式与原始信用风险数据集的格式相同。
然后，结果将通过“Web 服务输出”模块从 Web 服务返回给用户。

> [!TIP]
> 根据预测实验的配置方式，将返回[评分模型][score-model]模块的整个结果。 这包括所有输入数据以及信用风险值和评分概率。 如果希望返回不同的结果 - 例如，只返回信用风险值， 可以在[评分模型][score-model]和 **Web 服务输出**之间插入[投影列][project-columns]模块，用于排除不想要让 Web 服务返回的列。 
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

2. 输入一组数据，然后单击“确定”。 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>在机器学习 Web 服务门户中测试

1. 在 Web 服务的“仪表板”页上，单击“默认终结点”下的“测试预览”链接。 Azure 机器学习 Web 服务门户中的 Web 服务终结点测试页将打开，并要求提供服务的输入数据。 这些是在原始信用风险数据集中出现的列。

2. 单击“测试请求-响应”。 

### <a name="test-a-new-web-service"></a>测试新的 Web 服务

只能在 Azure 机器学习 Web 服务门户中测试新的 Web 服务。

1. 在 [Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户中，单击页面顶部的“测试”。 此时将打开“测试”页，可在其中输入服务的数据。 显示的输入字段对应于原始信用风险数据集中出现的列。 

2. 输入一组数据，然后单击“测试请求-响应”。

测试结果将显示在页面右侧的输出列中。 


## <a name="manage-the-web-service"></a>管理 Web 服务

### <a name="manage-a-classic-web-service-in-the-azure-classic-portal"></a>在 Azure 经典门户中管理经典 Web 服务

部署经典 Web 服务后，可以从 [Azure 经典门户](https://manage.windowsazure.com)管理它。

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)
2. 在 Microsoft Azure 服务面板中，单击“机器学习”
3. 单击你的工作区
4. 单击“Web 服务”选项卡
5. 单击创建的 Web 服务
6. 单击“默认”终结点

然后可以执行一些操作，例如监视 Web 服务的运行状况，以及通过更改服务可处理的并发调用数来调整性能。

有关详细信息，请参阅：

* [创建终结点](machine-learning-create-endpoint.md)
* [缩放 Web 服务](machine-learning-scaling-webservice.md)

### <a name="manage-a-classic-or-new-web-service-in-the-azure-machine-learning-web-services-portal"></a>在 Azure 机器学习 Web 服务门户中管理经典或新的 Web 服务

部署 Web 服务（经典或新式）后，可以从 [Microsoft Azure 机器学习 Web 服务门户](https://services.azureml.net/quickstart)管理它。

监视 Web 服务的性能：

1. 登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户
2. 单击“Web 服务”
3. 单击你的 Web 服务
4. 单击“仪表板”

- - -
**后续步骤：[访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)**

[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


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
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

