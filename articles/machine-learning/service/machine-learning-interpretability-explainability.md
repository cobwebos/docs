---
title: 模型可解释性
titleSuffix: Azure Machine Learning service
description: 了解如何解释为什么您的模型进行预测使用 Azure 机器学习 SDK。 它可以用于在培训和推理过程了解您的模型进行预测的方式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: cba46a277dfce93d0080d8f04a26fd135407de15
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536734"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务的模型 interpretability

在本文中，您将了解如何解释为什么您的模型进行预测与 Azure 机器学习 Python SDK 的各种 interpretability 包。

使用 SDK 中的类和方法，可以获取：
+ 功能原始和工程功能的重要性的值
+ Interpretability 在大规模情况下，实际的数据集上，在培训和推理。
+ 若要在定型时帮助你发现数据和说明中的模式中的交互式可视化效果

在开发周期的训练阶段，模型设计人员和评估者可使用 interpretability 输出模型的验证假设并与利益干系人建立信任。  它们也使用该模型的见解以进行调试，因此验证模型行为匹配其目标，并检查偏置。

在机器学习**功能**是用于预测目标数据点的数据字段。 例如，若要预测信用风险，可能使用年龄、 帐户大小和帐户年龄的数据字段。 在这种情况下，年龄、 帐户大小和帐户年龄都**功能**。 特征重要性告诉您每个数据字段会如何影响模型的预测。 例如，年龄可能很大程度使用在预测中时帐户大小和保留时间不预测准确性会显著影响。 此过程允许数据科学家以解释生成预测，以便利益干系人具有到哪些数据点是在模型中最重要的可见性。

使用这些工具，可以解释机器学习模型**上的所有数据从全球**，或**上的特定数据点的本地**易于使用且可缩放的方式使用先进的技术。

Interpretability 类均通过多个 SDK 包。 了解如何[适用于 Azure 机器学习安装 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)包含由 Microsoft 支持的功能的主包。

* `azureml.contrib.explain.model`预览并可以尝试的实验性功能。

* `azureml.train.automl.automlexplainer` 用于解释自动化的机器学习模型的包。

> [!IMPORTANT]
> 中的内容`contrib`命名空间不完全支持。 随着实验性功能变得成熟，将逐渐将它们移到的主命名空间。

## <a name="how-to-interpret-your-model"></a>如何解释您的模型

您可以应用 interpretability 类和方法来了解模型的全局行为或特定的预测。 以前称为全局解释，后者调用本地说明。

可以根据该方法是否是不可知的模型或模型特定还分类方法。 某些方法针对特定类型的模型。 例如，SHAP 的树说明仅适用于基于树的模型。 某些方法视为黑盒子，如模拟说明或 SHAP 的内核说明该模型。 `explain`包利用这些基于数据集、 模型类型和用例的不同方法。

输出为一组给定的模型如进行其预测方式的信息：
* 全局/本地相对特征重要性

* 全局/本地功能和预测的关系

### <a name="explainers"></a>Explainers

有两个组的 explainers:SDK 中的直接 Explainers 和 Meta Explainers。

__直接 explainers__来自集成的库。 SDK 可包装所有 explainers，以便它们公开的公共 API 和输出格式。 如果您是直接使用这些 explainers 起来更舒适，您可以直接调用它们而不是使用通用的 API 和输出格式。 在 SDK 中提供直接 explainers 列表如下：

* **SHAP 树说明**:SHAP 的树说明，重点介绍多项式时间快速 SHAP 值估计算法特定于树和树的整体。
* **SHAP 深入说明**:基于从 SHAP，深入说明说明"是使用 DeepLIFT SHAP NIPS 文章所述生成在连接使用高速近似算法来 SHAP 深度学习模型中的值。 支持 TensorFlow 模型和使用 TensorFlow 后端的 Keras 模型 （还有 PyTorch 的初步支持）"。
* **SHAP 内核说明**:SHAP 的内核说明使用专门加权的本地线性回归用于估算 SHAP 任何模型的值。
* **模拟说明**:模拟说明基于全局代理项模型的概念。 全局代理项模型是以尽可能准确地估计黑色方框模型的预测进行定型的本质上可解释模型。 数据科学家可以解释为代理项模型得出的黑色方框模型有关的结论。 可以使用以下可解释模型之一作为代理项模型：LightGBM (LinearExplainableModel)、 (LinearExplainableModel) 进行线性回归、 随机梯度下降可以解释模型 (SGDExplainableModel) 和决策树 (DecisionTreeExplainableModel)。


* **排列功能重要性说明**:排列特征重要性是一种技术，用于说明的灵感源自的分类和回归模型[Breiman 的随机林纸张](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf)（请参阅部分 10）。 在高级别，它的工作的方式是关注的通过随机随机排布数据一项功能，同时为整个数据集和计算多少性能指标会降低。 更大的更改，更重要的功能。

* **暗黄说明**(`contrib`):根据为浅，酸橙色说明使用先进的本地可解释不限模型的说明 （酸橙色） 算法来创建本地代理项的模型。 全局代理项与模型不同，酸橙色侧重于训练本地代理项模型来解释单个预测。
* **HAN 文本说明**(`contrib`):HAN 文本说明用于为给定的黑色框文本模型获取从文本数据的模型说明使用分层网络。 我们为在给定的教师模型的预测输出 HAN 代理项模型定型。 定型后全局在文本语料库，我们添加了特定文档的 fine-tune 步骤以提高准确性的说明。 HAN 使用两个关注层，使用双向 RNN 句子和词引起注意。 DNN 是教师模型定型并对特定文档进行微调，我们可以关注层从提取 word importances。 我们发现 HAN 是比酸橙色或 SHAP 更准确的文本数据，但成本更高的定型时间也方面。 但是，我们已训练时间改进了通过为用户提供选项来初始化通过手套字词嵌入网络，虽然仍很慢。 可以通过在远程 Azure GPU VM 上运行 HAN 显著改进的训练时间。 层次结构的文档分类 （Yang et 都 2016年） 注意网络中描述的 HAN 实现 ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf))。


__元 explainers__自动选择合适的直接说明并生成基于给定的模型和数据集的最佳说明信息。 元 explainers 利用所有的库 （SHAP、 酸橙色，模仿等） 已集成或开发。 以下是元 explainers SDK 中提供：

* **表格说明**:与表格数据集一起使用。
* **文本说明**:与文本数据集一起使用。
* **映像说明**:与图像数据集一起使用。

除了为元选择的直接 explainers、 元 explainers 开发的基础库之上的其他功能和通过直接 explainers 提高速度和可伸缩性。

当前`TabularExplainer`采用以下逻辑来调用直接 SHAP Explainers:

1. 如果它是基于树的模型，应用 SHAP `TreeExplainer`、 else
2. 如果它是 DNN 模型，应用 SHAP `DeepExplainer`、 else
3. 将其视为黑盒模型并将应用 SHAP `KernelExplainer`

内置的智能`TabularExplainer`将成为更复杂，因为更多库集成到 SDK，我们了解每个说明的优点和缺点。

`TabularExplainer` 通过直接 Explainers 已进行了重要的功能和性能增强功能：

* **初始化数据集的摘要**。 中的说明的速度最重要的情况下，我们汇总初始化数据集并生成一组较小的具有代表性的示例，这样可以加快全局和本地的说明。
* **评估数据集进行抽样**。 如果用户通过一大组评估示例中，但实际上并不需要所有这些要计算，可以设置采样参数为 true，则全局说明提高速度。

下图显示了当前结构直接和 meta explainers。

[![机器学习 Interpretability 体系结构](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支持的模型

在 Python 中的数据集训练所有模型`numpy.array`， `pandas.DataFrame`， `iml.datatypes.DenseData`，或`scipy.sparse.csr_matrix`格式受 interpretability`explain`的 sdk 包。

说明函数接受作为输入模型和管道。 如果提供一个模型，则模型必须实现的预测函数`predict`或`predict_proba`符合 Scikit 约定。 如果提供的管道 （管道脚本的名称），则说明函数假定正在运行的管道脚本返回预测。 我们支持通过 PyTorch、 TensorFlow 和深度学习框架的 Keras 训练的模型。

### <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`explain`包设计为使用这两个本地和远程计算目标。 如果本地运行，SDK 函数将不与任何 Azure 服务。 可以在 Azure 机器学习计算上远程运行说明并登录到 Azure 机器学习运行历史记录服务说明信息。 此信息在登录之后，报表和可视化效果中进行了说明上很容易下载用户分析的 Azure 机器学习工作区门户。

## <a name="interpretability-in-training"></a>Interpretability 培训

### <a name="train-and-explain-locally"></a>训练和本地说明

1. 训练的模型中的本地 Jupyter 笔记本。

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 调用说明：若要初始化的说明对象，您需要将您的模型和一些定型数据传递给说明的构造函数。 可以选择性地传递功能名称和输出类名称 （如果进行分类），用于使信息更丰富的说明和可视化效果中。 下面介绍了如何实例化说明对象使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)， [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)，并[PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py)本地。 `TabularExplainer` 正在调用下面三个 SHAP explainers 之一 (`TreeExplainer`， `DeepExplainer`，或`KernelExplainer`)，并自动选择最适合的选项为你的用例。 但是，可以直接调用每个其三个基础 explainers。

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   或

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. 获取全局功能重要性的值。

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. 获取局部特征重要性的值： 使用以下函数调用来解释的单个实例的组。 请注意，PFIExplainer 不支持本地的说明。

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    或

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>训练和远程解释

虽然您可以在 Azure 机器学习服务支持的各种计算目标上进行训练，本部分中的示例演示如何实现此目的的 Azure 机器学习计算目标。

1. 在本地 Jupyter 笔记本 (例如，run_explainer.py) 中创建训练脚本。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 按照上的说明[设置用于为模型定型的计算目标](how-to-set-up-training-targets.md#amlcompute)若要了解有关如何设置 Azure 机器学习计算用作计算目标和提交训练运行。

3. 下载本地 Jupyter 笔记本中的说明。

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>可视化效果

使用可视化效果面板来了解和解释您的模型：

### <a name="global-visualizations"></a>全局可视化效果

以下图表提供经过训练的模型及其预测和说明的全局视图。

|绘图|描述|
|----|-----------|
|数据浏览| 数据集并预测值的概述。|
|全局重要性|全局范围内显示的 top K (可配置 K) 重要的功能。 此图表可用于了解全局基础模型的行为。|
|说明探索|演示如何一项功能负责在模型的预测值 （或预测值的概率） 中进行更改。 |
|摘要| 使用跨所有数据点值的有符号的本地功能重要性来显示的每个功能影响的预测值的分布。|

[![全局可视化仪表板](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本地可视化效果

您可以单击任何单个数据点在前面的图形加载给定的数据点将局部特征重要性任何的绘图时间。

|绘图|描述|
|----|-----------|
|本地的重要性|全局范围内显示的 top K (可配置 K) 重要的功能。 此图表可用于了解特定的数据点上的基础模型的本地行为。|
|Perturbation 探索|可以更改所选的数据值的点，并观察这些更改将如何影响预测值的功能。|
|单个条件假定条件下 (ICE)| 可以从最小值的功能值更改为最大值，以查看数据点的预测功能发生更改时的变化。|

[![可视化效果的仪表板本地特征重要性](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![可视化效果的仪表板功能 Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![可视化效果的仪表板 ICE 绘图](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

请注意需要能够在 Jupyter 内核启动之前启用可视化仪表板的小组件扩展。

* Jupyter 笔记本

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter 实验室

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
若要加载的可视化效果的仪表板，请使用以下代码：

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>原始功能转换

（可选） 可以将你的功能转换管道传递到说明转换 （而非工程的特征） 副本接收的原始特征方面的说明。 如果您跳过这，说明提供了在工程特征方面的解释。

如中所述的其中一个受支持的转换的格式是相同[sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)。 一般情况下，只要它们对单个列，因此显然一对多支持任何转换。 

我们可以通过使用介绍原始特征`sklearn.compose.ColumnTransformer`或拟合的转换器元组的列表。 使用下面的单元格`sklearn.compose.ColumnTransformer`。 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])



# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], 
                                    initialization_examples=x_train, 
                                    features=dataset_feature_names, 
                                    classes=dataset_classes, 
                                    transformations=preprocessor) 
```

如果你想要运行该示例使用拟合的转换器元组的列表，请使用以下代码： 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], 
                                     initialization_examples=x_train, 
                                     features=dataset_feature_names, 
                                     classes=dataset_classes, 
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>在推断时 interpretability

说明可以与原始模型一起部署，并可用于在评分时间提供本地说明信息。 我们还提供轻量的评分 explainers，以使在推断 interpretability 时间更高的性能。 部署轻量的计分概要说明的过程类似于部署模型，包括以下步骤：




1. 创建说明对象 （例如，使用 TabularExplainer）：

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 创建使用说明对象计分概要说明：

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 配置和注册使用计分概要说明模型的图像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. [可选]从云中检索评分的说明和测试说明

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 将映像部署到计算目标：

   1. 创建计分概要文件 (在此步骤中之前, 按照中的步骤[部署模型与 Azure 机器学习服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)注册原始预测模型)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. 定义部署配置 （此配置取决于您的模型的要求。 下面的示例定义一个 CPU 核心和 1 GB 的内存使用的配置）

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. 创建具有环境依赖项的文件

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. 使用 g + + 安装创建自定义 dockerfile

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 部署创建的映像 (估计时间：5 分钟）

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. 测试部署

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 清理：若要删除已部署的 Web 服务，请使用 `service.delete()`。

## <a name="interpretability-in-automated-ml"></a>自动化机器学习中 interpretability

自动化的机器学习包含用于解释中自动训练模型的特征重要性的包。 此外，分类方案允许你检索类级别特征重要性。 有两种方法，以启用自动的机器学习中的此行为：

* 若要启用已训练的系综模型的特征重要性，请使用[ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py)函数。

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* 若要启用培训之前每次单独运行的特征重要性，设置`model_explainability`参数`True`中`AutoMLConfig`对象，并提供验证数据。 然后，使用[ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py)函数。

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

有关详细信息，请参阅[操作方法](how-to-configure-auto-train.md#explain-the-model-interpretability)上启用 interpretability 自动化的机器学习中的功能。

## <a name="next-steps"></a>后续步骤

若要查看一系列 Jupyter notebook 演示上面的说明，请参阅[Azure 机器学习 Interpretability 示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
