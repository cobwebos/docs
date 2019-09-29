---
title: 模型可解释性
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 解释模型进行预测的原因。 可以在定型和推理期间使用它来了解模型如何进行预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 703416d2df103c1ac8db06cbef43600058c250ef
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350472"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>模型 interpretability 与 Azure 机器学习

本文介绍了如何通过 Azure 机器学习 Python SDK 的各种 interpretability 包来解释模型进行预测的原因。

使用 SDK 中的类和方法，可以获取：
+ 原始和工程功能的功能重要性值
+ 在定型和推理期间，大规模 Interpretability 实际数据集。
+ 交互式可视化效果，可帮助发现数据中的模式以及定型时间的说明

在开发周期的培训阶段，模型设计器和计算器可以使用 interpretability 的模型输出来验证假设并与利益干系人建立信任。  它们还使用该模型进行调试，验证模型行为与它们的目标，并检查是否有偏差。

在机器学习中，**功能**是用于预测目标数据点的数据字段。 例如，要预测信用风险，可以使用 "年龄"、"帐户大小" 和 "帐户年龄" 数据字段。 在这种情况下，年龄、帐户大小和帐户期限都是**功能**。 功能重要性告诉您每个数据字段对模型预测的影响。 例如，在预测中，年龄可能会很高，而 "帐户大小" 和 "年龄" 不会显著影响预测准确性。 此过程允许数据科学家解释所产生的预测，使利益干系人能够查看模型中最重要的数据点。

使用这些工具，您可以使用一种易于使用且可缩放的方式，以**全局方式在所有数据上**或以**本地方式在特定数据点上**对机器学习模型进行说明。

Interpretability 类通过多个 SDK 包提供。 了解如何[为 Azure 机器学习安装 SDK 包](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)主包，其中包含 Microsoft 支持的功能。

* `azureml.contrib.explain.model`可以尝试的、预览和实验性功能。

* `azureml.train.automl.automlexplainer`用于解释自动机器学习模型的包。

> [!IMPORTANT]
> 命名空间中`contrib`的内容不完全受支持。 当实验性功能变得成熟时，它们会逐渐移到主命名空间。

## <a name="how-to-interpret-your-model"></a>如何解释模型

您可以应用 interpretability 类和方法来了解模型的全局行为或特定的预测。 前者称为全局说明，后者称为本地说明。

还可以根据方法是与模型无关还是特定于模型来分类这些方法。 某些方法面向特定类型的模型。 例如，SHAP 的树说明仅适用于基于树的模型。 某些方法将模型视为黑色框，如模仿说明或 SHAP 的内核说明。 `explain`该包利用了基于数据集、模型类型和用例的各种不同方法。

输出是有关给定模型如何进行预测的一组信息，如：
* 全局/局部相对功能重要性

* 全局/本地功能和预测关系

### <a name="explainers"></a>Explainers

有两组 explainers：在 SDK 中直接 Explainers 和元 Explainers。

__直接 explainers__来自集成库。 SDK 包装所有 explainers，使其公开公共 API 和输出格式。 如果直接使用这些 explainers，可以直接调用它们，而不是使用通用 API 和输出格式。 下面列出了 SDK 中可用的直接 explainers：

* **SHAP 树说明**：SHAP 的树说明，侧重于多项式时间快速 SHAP 值估算算法，特定于树和整体的树。
* **SHAP Deep 说明**：根据 SHAP 中的说明，Deep 说明 "是一种高速近似算法，适用于深度学习模型中的 SHAP 值，这些值建立在与 SHAP NIPS 纸张中所述 DeepLIFT 的连接上。 支持使用 TensorFlow 后端的 TensorFlow 模型和 Keras 模型（还支持 PyTorch 的初步支持） "。
* **SHAP 内核说明**：SHAP 的内核说明使用特殊的加权本地线性回归来估算任何模型的 SHAP 值。
* **模拟说明**：模拟说明基于全局代理项模型的概念。 全局代理项模型是一种固有的可解释模型，经过训练，可尽可能准确地估计黑色框模型的预测。 数据科研人员可以解释代理项模型，以绘制关于黑色框模型的结论。 您可以使用以下可解释模型之一作为代理模型：LightGBM （LinearExplainableModel）、线性回归（LinearExplainableModel）、随机梯度下降 explainable 模型（SGDExplainableModel）和决策树（DecisionTreeExplainableModel）。


* **排列特征重要性说明**：排列功能的重要性是一种技术，用于说明通过[Breiman 的随机林](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf)的方式（请参阅第10部分）可以激发的分类和回归模型。 从较高层次来看，它的工作方式是对整个数据集随机混排一项功能，并计算出相关性能指标降低的程度。 更改越大，该功能越重要。

* **酸橙色说明**(`contrib`):基于酸橙色，酸橙色说明使用先进的本地可解释模式说明（酸橙色）算法来创建本地代理项模型。 与全局代理项模型不同，酸橙色侧重于定型本地代理项模型来解释各个预测。
* 中文**文本说明**(`contrib`):汉语文本说明使用分层注意网络从给定的黑色框文本模型的文本数据中获取模型说明。 我们在给定的老师型号的预测输出上定型了汉语代理模型。 在整个文本语料库中进行全局定型后，我们为特定文档添加了微调步骤，以便提高说明的准确性。 汉语使用双向 RNN，其中包含两个注意层，用于句子和单词。 DNN 在教师模型上定型并对特定文档进行微调后，就可以从 "注意" 层中提取单词 "importances"。 对于文本数据，我们发现了汉语比酸橙色或 SHAP 更精确，但对于培训时间而言，它的成本更高。 但是，我们已通过向用户提供使用手套 word 嵌入初始化网络的选项（尽管速度仍很慢）来改进培训时间。 通过在远程 Azure GPU VM 上运行汉语，可以显著提高训练时间。 ["文档分类的分层注意事项网络" （"阳 et al，2016）"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)中介绍了汉语的实现。


__元 explainers__会自动选择合适的直接说明，并根据给定的模型和数据集生成最佳解释信息。 元 explainers 利用我们集成或开发的所有库（SHAP、酸橙色、模拟等）。 以下是 SDK 中可用的元 explainers：

* **表格说明**：用于表格数据集。
* **文本说明**：用于文本数据集。
* **Image 说明**：用于图像数据集。

除了直接 explainers 的元选择以外，meta explainers 还在基础库之上开发附加功能，并通过直接 explainers 提高速度和可伸缩性。

当前`TabularExplainer`采用以下逻辑调用直接 SHAP Explainers：

1. 如果它是基于树的模型，则应用 SHAP `TreeExplainer`，否则
2. 如果这是一个 DNN 的模型，则`DeepExplainer`应用 SHAP，否则
3. 将其视为一个黑白模型，并应用 SHAP`KernelExplainer`

随着`TabularExplainer`将更多库集成到 SDK 中，内置的智能将会变得更加复杂，并了解每个说明的优缺点。

`TabularExplainer`还通过直接 Explainers 实现了重大功能和性能增强：

* **初始化数据集的摘要**。 在最重要的解释速度的情况下，我们汇总了初始化数据集，并生成了一小部分代表性示例，这将加快全局和本地说明。
* **采样计算数据集**。 如果用户传入一组大量的评估样本，但实际上并不需要计算全部，则可以将采样参数设置为 true，以加快全局说明的速度。

下图显示了 direct 和元 explainers 的当前结构。

[![机器学习 Interpretability 体系结构](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>支持的模型

SDK `numpy.array`的 interpretability `iml.datatypes.DenseData` `scipy.sparse.csr_matrix` `pandas.DataFrame`包支持`explain`对 Python、、或格式的数据集进行训练的任何模型。

解释函数接受模型和管道作为输入。 如果提供了模型，则该模型必须实现预测函数`predict`或`predict_proba`符合 scikit-learn 约定。 如果提供了管道（管道脚本的名称），则说明函数假设正在运行的管道脚本返回一个预测。 我们支持通过 PyTorch、TensorFlow 和 Keras 深度学习框架训练的模型。

### <a name="local-and-remote-compute-target"></a>本地和远程计算目标

`explain`包设计用于本地和远程计算目标。 如果在本地运行，SDK 函数将不会与任何 Azure 服务联系。 可以在 Azure 机器学习计算上远程运行说明，并将说明信息记录到 Azure 机器学习运行历史记录服务中。 记录此信息后，可在 Azure 机器学习工作区门户上轻松获取有关说明的报表和可视化效果，以便进行用户分析。

## <a name="interpretability-in-training"></a>培训中的 Interpretability

### <a name="train-and-explain-locally"></a>本地训练和解释

1. 在本地 Jupyter 笔记本中训练模型。

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

2. 调用说明：若要初始化说明对象，需要将模型和一些定型数据传递给说明的构造函数。 您还可以选择传递功能名称和输出类名称（如果要进行分类），这将用于使您的说明和可视化内容更有信息。 下面介绍如何使用[TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)、 [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)和[PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py)本地实例化说明对象。 `TabularExplainer`正在调用下三个 SHAP explainers 之一（`TreeExplainer`、 `DeepExplainer`或`KernelExplainer`），并自动为用例选择最适合的一个。 但是，可以直接调用其三个基础 explainers 中的每一个。

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
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel,SGDExplainableModel
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

3. 获取全局功能重要性值。

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

4. 获取本地功能重要性值：使用以下函数调用说明单个实例或一组实例。 请注意，PFIExplainer 不支持本地说明。

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
    local_explanation = explainer.explain_local(x_test[0:5])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>远程训练和解释

尽管可以对 Azure 机器学习支持的各种计算目标进行定型，但本部分中的示例演示如何使用 Azure 机器学习计算目标来执行此操作。

1. 在本地 Jupyter 笔记本中创建训练脚本（例如 run_explainer. py）。

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

2. 按照为[模型定型设置计算目标](how-to-set-up-training-targets.md#amlcompute)中的说明，了解如何将 Azure 机器学习计算设置为计算目标并提交定型运行。

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

使用可视化面板来理解和解释模型：

### <a name="global-visualizations"></a>全局可视化

以下图形提供了定型模型的全局视图及其预测和说明。

|绘图|描述|
|----|-----------|
|数据浏览| 数据集概述以及预测值。|
|全局重要性|全局显示前 K （可配置的 K）重要功能。 此图表对于了解基础模型的全局行为很有用。|
|解释探索|演示如何使用某一功能来更改模型的预测值（或预测值的概率）。 |
|总结| 对所有数据点使用签名的本地功能重要性值，以显示每个特征对预测值的影响的分布情况。|

[![可视化面板全局](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本地可视化

您可以在上一图中的任何时间单击任意单个数据点，为给定的数据点加载本地功能重要的绘图。

|绘图|描述|
|----|-----------|
|本地重要性|全局显示前 K （可配置的 K）重要功能。 此图表用于了解特定数据点上基础模型的本地行为。|
|Perturbation 探索|允许您更改所选数据点的功能值，并观察这些更改将如何影响预测值。|
|单个条件预计（ICE）| 允许您将功能值从 "最小值" 更改为 "最大值"，以查看在功能更改时数据点的预测如何变化。|

[![可视化面板本地功能重要性](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![可视化面板功能 Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![可视化仪表板 ICE 绘图](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

请注意，在开始 Jupyter 内核之前，你将需要启用可视化仪表板的小组件扩展。

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
若要加载可视化仪表板，请使用以下代码：

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>原始功能转换

（可选）可以将功能转换管道传递给说明，以便在转换前通过原始功能（而不是工程功能）接收解释。 如果跳过此项，说明将提供工程功能的说明。

支持的转换的格式与[spark-sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)中描述的转换的格式相同。 通常情况下，只要转换在单个列上操作，就会支持任何转换，因此很明显都是一对多转换。 

可以通过使用`sklearn.compose.ColumnTransformer`或合适的转换器元组列表来解释原始功能。 下面的单元格`sklearn.compose.ColumnTransformer`使用。 

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

如果希望使用合适的转换器元组列表运行该示例，请使用以下代码： 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

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

## <a name="interpretability-at-inferencing-time"></a>推断时的 Interpretability

说明可以与原始模型一起部署，并且可以在评分时间使用以提供本地解释信息。 我们还提供了更轻量的评分 explainers，使 interpretability 的性能更高。 部署较轻量评分说明的过程与部署模型类似，包括以下步骤：




1. 创建解释对象（例如，使用 TabularExplainer）：

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用解释对象创建计分说明：

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 配置和注册使用计分说明模型的映像。

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 可有可无从云中检索评分说明并测试说明

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

   1. 创建评分文件（在此步骤之前，请按照[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)中的步骤来注册原始预测模型）

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

   1. 定义部署配置（此配置取决于模型的要求。 下面的示例定义了一个使用一个 CPU 核心和 1 GB 内存的配置

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

   1. 创建已安装 g + + 的自定义 dockerfile

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. 部署创建的映像（时间估算：5分钟）

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




## <a name="next-steps"></a>后续步骤

若要查看演示上述说明的 Jupyter 笔记本的集合，请参阅[Azure 机器学习 Interpretability 示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
