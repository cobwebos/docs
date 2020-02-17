---
title: 自动机器学习中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 获取有关自动 ML 模型如何确定功能重要性并进行预测的说明。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 9cacc0a1faa66e5d265f7f80830e13c54a88a68c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77366269"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>自动机器学习中的模型 interpretability

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure 机器学习中启用自动机器学习（ML）的 interpretability 功能。 自动 ML 可帮助你了解原始功能和工程设计的重要性。 若要使用 model interpretability，请在 `AutoMLConfig` 对象中设置 `model_explainability=True`。  

在本文中，学习如何：

- 在最佳模型或任何模型的培训过程中执行 interpretability。
- 启用可视化效果，以帮助你查看数据和说明中的模式。
- 在推理或评分过程中实现 interpretability。

## <a name="prerequisites"></a>必备条件

- Interpretability 功能。 运行 `pip install azureml-interpret azureml-contrib-interpret` 以获取所需的包。
- 构建自动 ML 试验的知识。 有关如何使用 Azure 机器学习 SDK 的详细信息，请完成此[回归模型教程](tutorial-auto-train-models.md)，或了解如何[配置自动 ML 试验](how-to-configure-auto-train.md)。

## <a name="interpretability-during-training-for-the-best-model"></a>最佳模型培训过程中的 Interpretability

从 `best_run`检索说明，其中包括工程功能和原始功能的说明。

### <a name="download-engineered-feature-importance-from-artifact-store"></a>从项目应用商店下载工程特征重要性

您可以使用 `ExplanationClient` 从 `best_run`的项目存储中下载工程功能说明。 若要获取 `raw=True`的原始功能集的说明。

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>在针对任何模型的培训期间 Interpretability 

计算模型说明并对其进行可视化时，并不限于自动 ML 模型的现有模型说明。 还可以使用不同的测试数据获取模型的说明。 本部分中的步骤说明如何根据测试数据计算和可视化工程功能重要性和原始功能的重要性。

### <a name="retrieve-any-other-automl-model-from-training"></a>从定型检索任何其他 AutoML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>设置模型说明

使用 `automl_setup_model_explanations` 获取经过工程处理和原始功能的说明。 `fitted_model` 可以生成以下各项：

- 来自定型或测试示例的特色数据
- 工程和原始功能名称列表
- 分类方案的标记列中的 Findable 类

`automl_explainer_setup_obj` 包含上述列表中的所有结构。

```python
from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化说明的模拟功能重要性

若要生成有关 AutoML 模型的说明，请使用 `MimicWrapper` 类。 可以用以下参数初始化 MimicWrapper：

- 说明安装程序对象
- 你的工作区
- 作为 `fitted_model` 自动 ML 模型的代理项的 LightGBM 模型

MimicWrapper 还获取将上传原始和工程说明的 `automl_run` 对象。

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>使用 MimicExplainer 计算和形象设计功能重要性

可以在 MimicWrapper 中调用 `explain()` 方法与转换后的测试示例，以获取生成的工程功能的重要性。 你还可以使用 `ExplanationDashboard` 通过自动 ML featurizers 查看生成的工程功能的功能重要性值的仪表板可视化。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>使用模拟说明计算和可视化原始特征重要性

您可以通过转换后的测试示例并将 `get_raw=True` 设置为获取原始功能的重要性功能，在 MimicWrapper 中再次调用 `explain()` 方法。 你还可以使用 `ExplanationDashboard` 查看原始功能的功能重要性值的仪表板可视化。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>推理过程中的 Interpretability

在本部分中，将了解如何使用说明（用于计算上一部分中的说明）操作自动 ML 模型。

### <a name="register-the-model-and-the-scoring-explainer"></a>注册模型和评分说明

使用 `TreeScoringExplainer` 创建将在推理时计算原始和工程功能重要性值的计分说明。 将计分说明初始化为之前计算的 `feature_map`。 计分说明使用 `feature_map` 返回原始功能重要性。

保存评分说明，然后将该模型和评分说明注册到模型管理服务。 运行以下代码：

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

接下来，在容器中为已部署的模型创建必要的环境依赖项。 请注意，azureml-默认情况下，版本 > = 1.0.45 必须作为 pip 依赖项列出，因为它包含将模型托管为 web 服务所需的功能。

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

使用 conda 文件和前面步骤中的计分文件来部署服务。

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

### <a name="inference-with-test-data"></a>包含测试数据的推理

用一些测试数据的推理来查看自动 ML 模型的预测值。 查看预测值的工程特征重要性和预测值的原始特征重要性。

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>可视化以发现数据中的模式和培训时的说明

可以在[Azure 机器学习 studio](https://ml.azure.com)中的工作区中可视化功能重要性图表。 自动 ML 运行完成后，请选择 "**查看模型详细信息**" 以查看特定的运行。 选择 "**解释**" 选项卡，查看 "解释可视化效果" 仪表板。

[![机器学习 Interpretability 体系结构](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>后续步骤

若要详细了解如何在自动化机器学习之外的 Azure 机器学习 SDK 区域中启用模型说明和功能重要性，请参阅[interpretability 上的概念文章](how-to-machine-learning-interpretability.md)。
