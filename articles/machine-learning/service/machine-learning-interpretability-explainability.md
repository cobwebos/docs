---
title: 模型可解释性
titleSuffix: Azure Machine Learning service
description: 了解如何解释为什么您的模型进行预测使用 Azure 机器学习 Interpretability SDK。 它可以用于在定型和推断过程了解您的模型进行预测的方式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817246"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务的模型 interpretability

在本文中，您将了解如何解释为什么您的模型进行预测其使用 Azure 机器学习 Interpretability SDK 进行的。 能够解释您的模型是重要原因如下：

* 客户和利益干系人想要知道**如果他们可以相信预测可让您的模型**。
* 你想要了解为数据科学家**如何查询模型以寻找见解**。 你还需要工具来上做出明智的决策**如何改进您的模型**。
* 作为一家公司，您需要了解**使用不同的模型的行为输入分发**并**模型的工作方式分析特定的输入时**。

机器学习 interpretability 非常重要的机器学习开发周期的两个阶段中： 

* 期间**培训**:模型设计人员和评估者需要 interpretability 工具以向利益干系人建立信任说明模型的输出。 它们还需要深入了解该模型，以便它们可以调试模型，并使决策的行为是否符合其目标。 最后，他们需要确保该模型不会有偏差。

* 期间**推断**:需要使用您的模型的人们可以解释预测。 例如，为什么没有模型拒绝抵押贷款时，或预测投资组合会带来较高风险？

Azure 机器学习 Interpretability SDK 集成了技术开发的 Microsoft 和经验证的第三方库 （例如，SHAP 和酸橙色）。 SDK 集成库之间创建一个通用 API，并将 Azure 机器学习服务集成。 使用此 SDK，可以解释机器学习模型**上的所有数据从全球**，或**上的特定数据点的本地**易于使用且可缩放的方式使用先进的技术。

## <a name="how-does-it-work"></a>工作原理

Azure 机器学习 Interpretability 可用于了解模型的全局行为或特定的预测。 以前称为全局解释，后者调用本地说明。

可以根据该方法是否是不可知的模型或模型特定还分类 azure 机器学习 Interpretability 方法。 某些方法针对特定类型的模型。 例如，SHAP 的树说明仅适用于基于树的模型。 某些方法视为黑盒子，如模拟说明或 SHAP 的内核说明该模型。 Azure 机器学习 Interpretability SDK 利用这些基于数据集、 模型类型和用例的不同方法。

Azure 机器学习 Interpretability 对模型进行其预测的方式返回一组的信息。 信息包括以下项：

* 全局/本地相对特征重要性
* 全局/本地功能和预测的关系

## <a name="architecture"></a>体系结构

Azure 机器学习 Interpretability SDK 是结构化到两个 Python 包：

* [azureml.explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -主包，其中包含由 Microsoft 支持的功能。
* `azureml.contrib.explain.model` -预览和可以尝试的实验性功能。

    > [!IMPORTANT]
    > Contrib 中的内容不完全支持。 在实验性功能变得成熟，它们将逐渐将转移到主要包。

### <a name="explainers"></a>Explainers

Azure 机器学习 Interpretability SDK 引入了两套 explainers:直接 Explainers 和 Meta Explainers。

__直接 explainers__来自集成的库。 SDK 可包装所有 explainers，以便它们公开的公共 API 和输出格式。 在 SDK 中提供直接 explainers 列表如下：

> [!TIP]
> 如果您是直接使用这些 explainers 起来更舒适，您可以直接调用它们而不是使用通用的 API 和输出格式。

* **树说明**:SHAP 的树说明，重点介绍多项式时间快速 SHAP 值估计算法特定于树和树的整体。
* **深入说明**:基于从 SHAP，深入说明说明"是使用 DeepLIFT SHAP NIPS 文章所述生成在连接使用高速近似算法来 SHAP 深度学习模型中的值。 支持 TensorFlow 模型和使用 TensorFlow 后端的 Keras 模型 （还有 PyTorch 的初步支持）"。
* **内核说明**:SHAP 的内核说明使用专门加权的本地线性回归用于估算 SHAP 任何模型的值。
* **模拟说明**:模拟说明基于全局代理项模型的概念。 全局代理项模型是以尽可能准确地估计黑色方框模型的预测进行定型的本质上可解释模型。 数据科学家可以解释为代理项模型得出的黑色方框模型有关的结论。
* **暗黄说明**:根据为浅，酸橙色说明使用先进的本地可解释不限模型的说明 （酸橙色） 算法来创建本地代理项的模型。 全局代理项与模型不同，酸橙色侧重于训练本地代理项模型来解释单个预测。
* **HAN 文本说明**:HAN 文本说明用于为给定的黑色框文本模型获取从文本数据的模型说明使用分层网络。 我们为在给定的教师模型的预测输出 HAN 代理项模型定型。 定型后全局在文本语料库，我们添加了特定文档的 fine-tune 步骤以提高准确性的说明。 HAN 使用两个关注层，使用双向 RNN 句子和词引起注意。 DNN 是教师模型定型并对特定文档进行微调，我们可以关注层从提取 word importances。 我们发现 HAN 是比酸橙色或 SHAP 更准确的文本数据，但成本更高的定型时间也方面。 但是，我们已训练时间改进了通过为用户提供选项来初始化通过手套字词嵌入网络，虽然仍很慢。 可以通过在远程 Azure GPU VM 上运行 HAN 显著改进的训练时间。 层次结构的文档分类 （Yang et 都 2016年） 注意网络中描述的 HAN 实现 ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf))。

__元 explainers__自动选择合适的直接说明并生成基于给定的模型和数据集的最佳说明信息。 元 explainers 利用所有的库 （SHAP、 酸橙色，模仿等） 已集成或开发。 以下是元 explainers SDK 中提供：

* **表格说明**:与表格数据集一起使用。
* **文本说明**:与文本数据集一起使用。

除了为元选择的直接 explainers、 元 explainers 开发的基础库之上的其他功能和通过直接 explainers 提高速度和可伸缩性。

当前`TabularExplainer`采用以下逻辑来调用直接 Explainers:

1. 如果它是基于树的模型，应用`TreeExplainer`、 else
2. 如果它是 DNN 模型，应用`DeepExplainer`、 else
3. 将其视为黑盒模型和应用 `KernelExplainer`

内置的智能`TabularExplainer`将成为更复杂，因为更多库集成到 SDK，我们了解每个说明的优点和缺点。

`TabularExplainer` 通过直接 Explainers 已进行了重要的功能和性能增强功能：

* **初始化数据集的摘要**。 中的说明的速度最重要的情况下，我们汇总初始化数据集并生成一组较小的具有代表性的示例，这样可以加快全局和本地的说明。
* **评估数据集进行抽样**。 如果用户通过一大组评估示例中，但实际上并不需要所有这些要计算，可以设置采样参数为 true，则全局说明提高速度。

下图显示了两组直接与元 explainers 之间的关系。

[![机器学习 Interpretability 体系结构](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>支持的模型

在 Python 中的数据集训练所有模型`numpy.array`， `pandas.DataFrame`， `iml.datatypes.DenseData`，或`scipy.sparse.csr_matrix`格式支持的机器学习 Interpretability SDK。

说明函数接受作为输入模型和管道。 如果提供一个模型，则模型必须实现的预测函数`predict`或`predict_proba`符合 Scikit 约定。 如果提供的管道 （管道脚本的名称），则说明函数假定正在运行的管道脚本返回预测。

### <a name="local-and-remote-compute-target"></a>本地和远程计算目标

机器学习 Interpretability SDK 用于处理这两个本地和远程计算目标。 如果本地运行，SDK 函数将不与任何 Azure 服务。 可以在 Azure 机器学习计算上远程运行说明并登录到 Azure 机器学习运行历史记录服务说明信息。 此信息在登录之后，报表和可视化效果中进行了说明是 Azure 机器学习工作区门户上随时可供用户分析。

## <a name="train-and-explain-locally"></a>训练和本地说明

1. 训练的模型中的本地 Jupyter 笔记本。 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 调用说明：若要启动的说明对象，您需要传递模型、 定型数据、 感兴趣 （可选） 和输出的类名的功能 (如果分类) 到说明。 下面介绍了如何实例化说明对象使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)， [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)，和`LimeExplainer`本地。 `TabularExplainer` 正在调用下面三个 explainers 之一 (`TreeExplainer`， `DeepExplainer`，或`KernelExplainer`)，并自动选择最适合的选项为你的用例。 但是，可以直接调用每个其三个基础 explainers。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    或
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. 获取全局功能重要性的值。

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. 本地功能重要性值： 使用以下函数调用来解释的单个实例的组。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    或
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>训练和远程解释

虽然您可以在 Azure 机器学习服务支持的各种计算目标上进行训练，本部分中的示例演示如何使用 AMLCompute 执行此操作。

1. 在本地 Jupyter 笔记本 (例如，run_explainer.py) 中创建训练脚本。

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 按照上的说明[设置用于为模型定型的计算目标](how-to-set-up-training-targets.md#amlcompute)若要了解有关如何设置 Azure 机器学习计算用作计算目标和提交训练运行。

3. 下载本地 Jupyter 笔记本中的说明。 
    > [!IMPORTANT]
    > Contrib 中的内容不完全支持。 在实验性功能变得成熟，它们将逐渐将转移到主要包。

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>后续步骤

若要查看一系列 Jupyter notebook 演示上面的说明，请参阅[Azure 机器学习 Interpretability 示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
