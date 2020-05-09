---
title: 自动 ML 中的 Explainability
titleSuffix: Azure Machine Learning
description: 了解使用 Azure 机器学习 SDK 时如何获取解释，以了解自动化 ML 模型如何确定特征重要性并做出预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: e0ec6cbc4cea926dfc50cdae247aea5d765c20ca
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691219"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Interpretability：自动机器学习中的模型说明

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure 机器学习中获取自动机器学习（ML）的说明。 自动化 ML 可帮助你了解工程特征重要性。 

默认情况下，1.0.85 之后的所有 SDK 版本设置 `model_explainability=True`。 在 SDK 版本 1.0.85 及更早版本中，用户需要在 `model_explainability=True` 对象中设置 `AutoMLConfig`，才能使用模型可解释性。 

在本文中，学习如何：

- 在训练最佳模型或任意模型过程中执行解释。
- 启用可视化效果，以帮助查看数据和解释中的模式。
- 在推理或评分过程中实现可解释性。

## <a name="prerequisites"></a>必备条件

- 可解释性特征。 运行 `pip install azureml-interpret azureml-contrib-interpret` 获取所需的包。
- 生成自动化 ML 试验的知识。 有关如何使用 Azure 机器学习 SDK 的详细信息，请完成此[回归模型教程](tutorial-auto-train-models.md)，或参阅如何[配置自动化 ML 试验](how-to-configure-auto-train.md)。

## <a name="interpretability-during-training-for-the-best-model"></a>训练最佳模型过程中的可解释性

从 `best_run` 中检索解释，其中包括工程特征的解释。

### <a name="download-engineered-feature-importance-from-artifact-store"></a>从项目存储下载工程特征重要性

可以使用 `ExplanationClient` 从 `best_run` 的项目存储下载工程特征解释。 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>训练任意模型过程中的可解释性 

计算模型解释并将其可视化时，并不局限于自动化 ML 模型的现有模型解释。 还可以获取使用不同测试数据的模型的解释。 本部分中的步骤说明如何根据测试数据计算工程特征重要性并将其可视化。

### <a name="retrieve-any-other-automl-model-from-training"></a>从训练中检索任何其他自动化 ML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>设置模型解释

使用 `automl_setup_model_explanations` 获取工程解释。 `fitted_model` 可生成以下项：

- 从已训练的样本或测试样本生成有特征的数据
- 工程特征名称列表
- 分类方案中带标签列内的可查找类

`automl_explainer_setup_obj` 包含上述列表中的所有结构。

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化模拟解释器以计算特征重要性

若要生成自动化 ML 模型的解释，请使用 `MimicWrapper` 类。 可使用以下参数初始化 MimicWrapper：

- 解释器设置对象
- 工作区
- 用于解释`fitted_model`自动 ML 模型的代理项模型

MimicWrapper 还获取 `automl_run` 对象，工程解释将上传到该对象。

```python
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>使用 MimicExplainer 来计算并可视化特征重要性

可以结合转换的测试样本在 MimicWrapper 中调用 `explain()` 方法，以获取生成的工程特征的特征重要性。 还可以使用 `ExplanationDashboard` 通过自动化 ML 特征化器来查看生成的工程特征的特征重要性值仪表板可视化效果。

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>推理过程中的可解释性

本部分介绍如何使用解释器（在上一部分用于计算解释）将自动化 ML 模型操作化。

### <a name="register-the-model-and-the-scoring-explainer"></a>注册模型和评分解释器

使用 `TreeScoringExplainer` 创建评分解释器，用于在推理时计算工程特征重要性值。 使用前面计算出的 `feature_map` 初始化评分解释器。 

保存评分解释器，然后将模型和评分解释器注册到模型管理服务。 运行以下代码：

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>创建用于设置服务的 conda 依赖项

接下来，在容器中为已部署的模型创建所需的环境依赖项。 请注意，版本为 1.0.45 或更高的 azureml-defaults 必须列为 pip 依赖项，因为它包含将模型托管为 Web 服务所需的功能。

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>部署服务

使用前面步骤中所述的 conda 文件和评分文件来部署服务。

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>使用测试数据执行推理

使用一些测试数据执行推理，以查看自动化 ML 模型提供的预测值。 查看预测值的工程特征重要性。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>在训练时进行可视化以发现数据和解释中的模式

可以在 [Azure 机器学习工作室](https://ml.azure.com)中的工作区内可视化特征重要性图表。 自动化 ML 运行完成后，选择“查看模型详细信息”以查看特定的运行。  选择“解释”选项卡以查看解释可视化仪表板。 

[![机器学习可解释性体系结构](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>后续步骤

有关如何在 Azure 机器学习 SDK 区域（而不是自动化机器学习）中启用模型解释和特征重要性的详细信息，请参阅[有关可解释性的概念文章](how-to-machine-learning-interpretability.md)。
