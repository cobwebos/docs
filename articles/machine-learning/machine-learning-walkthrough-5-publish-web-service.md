---
title: "步骤 5：部署机器学习 Web 服务 | Microsoft 文档"
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
ms.date: 10/05/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: c7e5bf29aacbcb11bfa1db01738d234ae64b46b2
ms.openlocfilehash: 141586d880e6a8f069ec26b3279d9787c5e046b4


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
为了让其他人可以使用此演练中开发的预测模型，我们将它部署为 Azure 上的 Web 服务。

截至目前，我们一直在试验如何训练模型。 但是已部署的服务不再进行训练，它会根据模型，通过对用户的输入进行评分来生成预测。 因此我们需要做好一些准备工作，将此试验从***训练***试验转换为***预测***试验。 

此过程由两个步骤组成：  

1. 将创建的*训练试验*转换为*预测试验*
2. 将预测试验部署为 Web 服务

但首先，需要稍微修整一下此试验。 目前试验中有两个不同的模型，但将试验部署为 Web 服务时，只需要一个模型。  

假设已判定推升树模型是更适合使用的模型。 因此，首先是删除[双类支持向量机][two-class-support-vector-machine]模块，以及用于训练该模块的其他模块。 可能需要单击试验画布底部的“另存为”，先创建一个试验副本。

需要删除以下模块：  

* [双类支持向量机][two-class-support-vector-machine]
* 与它连接的[训练模型][train-model]和[评分模型][评分模型]模块
* [规范化数据][normalize-data]（两者）
* [评估模型][评估模型]

选择模块然后按 Delete 键，或右键单击模块并选择“删除”。

现已准备好使用[双类提升决策树][two-class-boosted-decision-tree]部署此模型。

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>将训练实验转换为预测实验
转换为预测试验的过程包括三个步骤：

1. 保存已训练的模型，然后替换训练模块
2. 修整试验，删除只有训练需要的模块
3. 定义 Web 服务接受输入的位置以及生成输出的位置

幸好上述三个步骤只需单击试验画布底部的“设置 Web 服务”即可完成（选择“预测 Web 服务”选项）。

单击“设置 Web 服务”时，会发生以下情况：

* 训练的模型已另存为试验画布左侧模块控制板中的单个“已训练模型”模块（可以在“已训练模型”下找到此模块）。
* 用于训练的模块被删除。 具体而言：
  * [双类提升决策树][two-class-boosted-decision-tree]
  * [训练模型][train-model]
  * [拆分数据][split]
  * 用于测试数据的第二个[执行 R 脚本][execute-r-script]模块
* 保存的训练模型已添加回到试验中。
* 已添加 **Web 服务输入**和 **Web 服务输出**模块。

> [!NOTE]
> 试验已保存在已添加到试验画布顶部的选项卡下的两个部分中：原始训练试验位于“训练试验”选项卡下面，新建的预测试验位于“预测试验”下面。
> 
> 

需要对此特定试验执行一个额外的步骤。
已添加两个[执行 R 脚本][execute-r-script]模块来向数据提供加权函数以进行训练和测试。 在最终的模型中无需这样做。

机器学习工作室已在删除[拆分][split]模块时删除[执行 R 脚本][execute-r-script]模块。 现在可以删除另一个模块，并直接将[元数据编辑器][metadata-editor]连接到[评分模型][评分模型]。    

试验现在应如下所示：  

![为训练的模型评分][4]  

> [!NOTE]
> 你可能会惊讶我们为何要在预测试验中留下“UCI 德国信用卡数据”数据集。 服务使用的是用户数据，而不是原始数据集，为何要让原始数据集保留在模型中？
> 
> 服务的确无需原始信用卡数据， 但需要该数据的架构，例如，有多少个列及哪些列是数值等信息。 需要此架构信息才能解释用户的数据。 保留连接这些组件的目的是在运行服务时，评分模型可获得数据集架构。 使用的不是数据，而只是架构。  
> 
> 

最后一次运行试验（单击“运行”）。若要验证模型是否仍然正常工作，请单击[评分模型][评分模型]模块的输出，然后选择“查看结果”。 此时将显示原始数据，以及信用风险值（“评分标签”）和评分概率值（“评分概率”）。 

## <a name="deploy-the-web-service"></a>部署 Web 服务
可以基于 Azure Resource Manager，将试验部署为经典 Web 服务或新式 Web 服务。

### <a name="deploy-as-a-classic-web-service"></a>部署为经典 Web 服务
若要部署派生自试验的经典 Web 服务，请单击画布下面的“部署 Web 服务”，然后选择“部署 Web 服务 [经典]”。 机器学习工作室会将试验部署为 Web 服务，并将你转到该 Web 服务的仪表板。 在这里可以返回试验（“查看快照”或“查看最新”），以及运行简单的 Web 服务测试（请参阅下面的**测试 Web 服务**）。 此处，还提供了有关创建可访问 Web 服务的应用程序的信息（本演练的下一步骤提供了更多相关信息）。

![Web 服务仪表板][6]

可以单击“配置”选项卡来配置服务。 可以在此处修改服务名称（默认指定试验名称）并输入说明。 此外，可为输入和输出列指定更友好的标签。  

![配置 Web 服务][5]  

### <a name="deploy-as-a-new-web-service"></a>部署为新式 Web 服务
若要部署派生自试验的新式 Web 服务，请单击画布下面的“部署 Web 服务”，然后选择“部署 Web 服务 [新]”。 机器学习工作室会将你转到 Azure 机器学习 Web 服务的“部署试验”页。

输入 Web 服务的名称，并选择定价计划。 如果你有现有的定价计划，可以选择它，否则你必须为该服务创建新的定价计划。 

1. 在“定价计划”下拉菜单中选择一个现有计划，或选择“选择新计划”选项。
2. 在“计划名称”中，键入用于标识帐单上的计划的名称。
3. 从“每月计划层”中选择一个计划层。 计划层默认为默认区域的计划，并且 Web 服务将部署到该区域。

单击“部署”和“快速入门”页面，打开 Web 服务。

可以单击“配置”菜单选项来配置服务。 可以在此处修改服务名称（默认指定试验名称）并输入说明。 

若要测试 Web 服务，请单击“测试”菜单选项（请参阅下面的**测试 Web 服务**）。 有关创建可访问 Web 服务的应用程序的信息，请单击“使用”菜单选项（本演练的下一步骤提供了更多相关信息）。

> [!TIP]
> 部署 Web 服务后可以更新它。 例如，如果想要更改模型，请编辑训练试验、调整模型参数，然后单击“部署 Web 服务”。 然后选择“部署 Web 服务 [经典]”或“部署 Web 服务 [新]”。 重新部署试验时，该试验将替换 Web 服务，然后，使用的就是已更新的模型了。  
> 
> 

## <a name="test-the-web-service"></a>测试 Web 服务
### <a name="test-a-classic-web-service"></a>测试经典 Web 服务
可以在机器学习工作室或 Azure 机器学习 Web 服务门户中测试服务。 在 Azure 机器学习 Web 服务门户中测试的好处是可以启用 

**在机器学习工作室中测试**

在“仪表板”页上，单击“默认终结点”下的“测试”按钮。 此时将弹出一个对话框，要求提供服务的输入数据。 这就是在原始德国信用风险数据集中出现的列。  

输入一组数据，然后单击“确定”。 

**在 Azure 机器学习 Web 服务门户中测试**

在“仪表板”页上，单击“默认终结点”下的“测试”预览链接。 Azure 机器学习 Web 服务门户中的 Web 服务终结点测试页将打开，并要求提供服务的输入数据。 这就是在原始德国信用风险数据集中出现的列。

单击“测试请求-响应”。 

在 Web 服务中，数据经过 **Web 服务输入**模块，再经过[元数据编辑器][metadata-editor]模块，最后进入[评分模型][评分模型]模块并在其中评分。 然后，通过 **Web 服务输出**从 Web 服务输出结果。

**测试新式 Web 服务**

在 Azure 机器学习 Web 服务门户中，单击页面顶部的“测试”。 此时将打开“测试”页，可在其中输入服务的数据。 显示的输入字段对应于原始德国信用风险数据集中出现的列。 

输入一组数据，然后单击“测试请求-响应”。

测试结果将显示在页面右侧的输出列中。 

在 Azure 机器学习 Web 服务门户中测试时，可以启用示例数据来测试请求-响应服务。 如果在机器学习工作室中创建 Web 服务，示例数据来自用于训练模型的数据。

> [!TIP]
> 根据预测试验的配置方式，将返回[评分模型][评分模型]模块的整个结果。 这包括所有输入数据以及信用风险值和评分概率。 如果希望返回不同的结果（例如只要信用风险值），可以在[评分模型][评分模型]和 **Web 服务输出**之间插入[投影列][project-columns]模块，用于排除不想要让 Web 服务返回的列。 
> 
> 

## <a name="manage-the-web-service"></a>管理 Web 服务
**管理经典 Web 服务**

部署经典 Web 服务后，可以从 [Azure 经典门户](https://manage.windowsazure.com)管理它。

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在 Microsoft Azure 服务面板中，单击“**机器学习**”。
3. 单击你的工作区。
4. 单击“Web 服务”选项卡。
5. 单击创建的 Web 服务。
6. 单击“默认”终结点。

然后可以执行一些操作，例如监视 Web 服务的运行状况，以及通过更改服务可处理的并发调用数来调整性能。

有关详细信息，请参阅：

* [创建终结点](machine-learning-create-endpoint.md)
* [缩放 Web 服务](machine-learning-scaling-webservice.md)

**在 Azure 机器学习 Web 服务门户中管理 Web 服务**

部署 Web 服务（经典或新式）后，可以从 [Azure 机器学习 Web Services 门户](https://servics.azureml.net)管理它。

监视 Web 服务的性能：

1. 登录到 [Azure 机器学习 Web 服务门户](https://servics.azureml.net)。
2. 单击“Web 服务”。
3. 单击你的 Web 服务。
4. 单击“仪表板”。

- - -
**后续步骤：[访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[评估模型]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[评分模型]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/



<!--HONumber=Nov16_HO4-->


