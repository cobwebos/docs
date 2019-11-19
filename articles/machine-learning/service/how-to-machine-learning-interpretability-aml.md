---
title: 针对本地和远程运行的模型 interpretability
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 来了解机器学习模型如何确定功能重要性并做出预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: ffb9e0547c44ee47a43de00e51933ce7d0584759
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158725"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>针对本地和远程运行的模型 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure 机器学习 Python SDK 的 interpretability 包来了解模型为何会进行预测。 学习如何：

* 解释在本地和远程计算资源上定型的机器学习模型。
* 在 Azure 运行历史记录中存储本地和全局说明。
* 在[Azure 机器学习 studio](https://ml.azure.com)中查看 interpretability 的可视化效果。
* 使用模型部署计分说明。

有关详细信息，请参阅[Model interpretability in Azure 机器学习 service](how-to-machine-learning-interpretability.md)。

## <a name="local-interpretability"></a>本地 interpretability

下面的示例演示如何在不联系 Azure 服务的情况下在本地使用 interpretability 包。

1. 如果需要，请使用 `pip install azureml-interpret` 获取 interpretability 包。

1. 训练本地 Jupyter 笔记本中的示例模型。

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

1. 在本地调用说明。
   * 若要初始化说明对象，请将模型和一些定型数据传递给说明的构造函数。
   * 若要使您的说明和可视化效果更丰富，可以选择在进行分类时传入功能名称和输出类名称。

   以下代码块显示了如何在本地实例化带有 `TabularExplainer`、`MimicExplainer`和 `PFIExplainer` 的说明对象。
   * `TabularExplainer` 调用下三个 SHAP explainers 之一（`TreeExplainer`、`DeepExplainer`或 `KernelExplainer`）。
   * `TabularExplainer` 会自动为用例选择最适合的一个，但你可以直接调用其三个基础 explainers。

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    或

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>整体，全局特征重要性值

请参阅以下示例，帮助你获取全局功能重要性值。

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

### <a name="instance-level-local-feature-importance-values"></a>实例级别的本地功能重要性值

通过调用单个实例或一组实例的解释来获取本地功能重要性值。
> [!NOTE]
> `PFIExplainer` 不支持本地说明。

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>远程运行的 Interpretability

下面的示例演示如何使用 `ExplanationClient` 类为远程运行启用 model interpretability。 它在概念上类似于本地过程，只不过：

* 使用远程运行中的 `ExplanationClient` 上传 interpretability 上下文。
* 稍后在本地环境中下载上下文。

1. 如果需要，请使用 `pip install azureml-contrib-interpret` 获取所需的包。

1. 在本地 Jupyter 笔记本中创建训练脚本。 例如，`train_explain.py`。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. 将 Azure 机器学习计算设置为计算目标，并提交训练运行。 有关说明，请参阅[设置用于模型定型的计算目标](how-to-set-up-training-targets.md#amlcompute)。 你还可能会发现[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)有帮助。

1. 下载本地 Jupyter 笔记本中的说明。

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

## <a name="raw-feature-transformations"></a>原始功能转换

您可以选择在原始、未经转换功能而不是工程功能方面获得说明。 对于此选项，请将功能转换管道传递到 `train_explain.py`中的说明。 否则，说明会根据设计的功能提供说明。

支持的转换的格式与[spark-sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)中所述的格式相同。 通常情况下，只要对单个列执行转换，就会支持任何转换，以便清楚地将其视为一对多。

使用 `sklearn.compose.ColumnTransformer` 或适用的转换器元组列表获取对原始功能的说明。 下面的示例使用 `sklearn.compose.ColumnTransformer`。

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

## <a name="visualizations"></a>可视化效果

在本地 Jupyter 笔记本中下载说明后，可以使用可视化仪表板来了解和解释模型。

### <a name="global-visualizations"></a>全局可视化

以下图形提供了定型模型的全局视图及其预测和说明。

|出|说明|
|----|-----------|
|数据浏览| 显示数据集的概述以及预测值。|
|全局重要性|全局显示 top K （可配置的 K）重要功能。 有助于理解基础模型的全局行为。|
|解释探索|说明功能如何影响模型的预测值的变化或预测值的概率。 显示功能交互的影响。|
|摘要重要性|在所有数据点使用本地的功能重要性值，以显示每个功能对预测值的影响。|

[全局 ![可视化仪表板](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>本地可视化

您可以通过在绘图中选择单个数据点，为任何数据点加载本地功能重要的绘图。

|出|说明|
|----|-----------|
|本地重要性|全局显示前 K （可配置的 K）重要功能。 有助于说明特定数据点上基础模型的本地行为。|
|Perturbation 探索|允许更改所选数据点的功能值，并观察对预测值所做的更改。|
|单个条件预计（ICE）| 允许从最小值到最大值的功能值更改。 有助于说明在功能发生变化时数据点的预测如何变化。|

[![可视化面板本地功能重要性](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![可视化面板功能 Perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![可视化仪表板 ICE 绘图](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

> [!NOTE]
> 在 Jupyter 内核开始之前，请确保为可视化仪表板启用小组件扩展。

* Jupyter 笔记本

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

若要加载可视化仪表板，请使用以下代码：

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure 机器学习 studio 中的可视化效果

如果完成[远程 interpretability](#interpretability-for-remote-runs)步骤，可以在[Azure 机器学习 studio](https://ml.azure.com)中查看可视化仪表板。 此仪表板是上面所述的可视化面板的更简单版本。 它仅支持两个选项卡：

|出|说明|
|----|-----------|
|全局重要性|全局显示 top K （可配置的 K）重要功能。 有助于理解基础模型的全局行为。|
|摘要重要性|在所有数据点使用本地的功能重要性值，以显示每个功能对预测值的影响。|

如果全局和本地说明都可用，则数据将填充这两个选项卡。 如果只有全局说明，则 "摘要重要性" 选项卡处于禁用状态。

按照以下路径之一访问 Azure 机器学习 studio 中的可视化仪表板：

* **试验**窗格（预览版）
  1. 在左窗格中选择 "**试验**"，查看在 Azure 机器学习服务上运行的实验的列表。
  1. 选择特定实验以查看该实验中的所有运行。
  1. 选择一个 "运行"，然后**选择 "说明" 选项卡**，然后单击 "解释可视化" 仪表板。

   [![可视化面板本地功能重要性](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)

* **模型**窗格
  1. 如果您按照[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)中的步骤注册了原始模型，则可以在左窗格中选择**模型**来查看。
  1. 选择模型，然后单击 "**解释**" 选项卡，查看 "解释可视化效果" 仪表板。

## <a name="interpretability-at-inference-time"></a>在推理时间 Interpretability

可以将说明与原始模型一起部署，并在推断时使用它来提供本地解释信息。 我们还提供了更轻量的评分 explainers，提高推断时间的 interpretability 性能。 部署较轻量评分说明的过程与部署模型类似，包括以下步骤：

1. 创建解释对象。 例如，可以使用 `TabularExplainer`：

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 使用解释对象创建计分说明。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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

1. 作为一个可选步骤，您可以从云中检索评分说明并测试说明。

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 按照以下步骤将映像部署到计算目标：

   1. 如果需要，请按照[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)中的步骤注册原始预测模型。

   1. 创建评分文件。

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
   1. 定义部署配置。

         此配置取决于模型的要求。 下面的示例定义一个配置，该配置使用一个 CPU 内核和 1 GB 的内存。

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 创建具有环境依赖项的文件。

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. 创建一个已安装 g + + 的自定义 dockerfile。

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 部署已创建的映像。
   
         此过程大约需五分钟。

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

1. 测试部署。

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

1. 清理。

   若要删除已部署的 Web 服务，请使用 `service.delete()`。

## <a name="next-steps"></a>后续步骤

[了解有关模型 interpretability 的详细信息](how-to-machine-learning-interpretability.md)