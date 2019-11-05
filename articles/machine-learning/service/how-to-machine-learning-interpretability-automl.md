---
title: 自动 ML 中的模型 interpretability
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SDK 解释自动 ML 模型进行预测的原因。 在定型和推理过程中，可以使用它来了解模型如何确定特征重要性并进行预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515325"
---
# <a name="model-interpretability-for-automated-ml-models"></a>自动 ML 模型的模型 interpretability

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本操作方法指南中，你将了解如何使用 Azure 机器学习服务在自动化机器学习中启用 interpretability 功能。 自动 ML 允许您了解原始功能和工程设计方面的重要性。 若要使用 model interpretability，请在 `AutoMLConfig` 对象中设置 `model_explainability=True`。  

本文介绍以下任务：

* 最佳模型或任何模型的培训过程中的 Interpretability
* 启用可视化效果，以帮助发现数据中的模式和说明
* 推理或评分过程中的 Interpretability

## <a name="prerequisites"></a>必备组件

* 运行 `pip install azureml-interpret azureml-contrib-interpret` 以获取 interpretability 功能所需的包。
* 本文假设你知道如何构建自动 ML 试验。 若要了解如何在 SDK 中使用自动 ML，请参阅[教程](tutorial-auto-train-models.md)或操作[方法](how-to-configure-auto-train.md)。
 
## <a name="interpretability-during-training-for-the-best-model"></a>最佳模型培训过程中的 Interpretability 

从 `best_run`检索说明，其中包括工程功能和原始功能的说明。 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>从项目应用商店下载工程特征重要性

你可以使用 `ExplanationClient` 从 best_run 的项目存储中下载工程功能说明。 若要获取 `raw=True`的原始功能集的说明。 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>在针对任何模型的培训期间 Interpretability 

本部分介绍如何计算模型说明并直观显示说明。 除了检索自动 ML 模型的现有模型说明以外，还可以使用不同的测试数据对模型进行说明。 通过以下步骤，您可以基于测试数据计算和可视化工程功能重要性和原始功能的重要性。

### <a name="retrieve-any-other-automl-model-from-training"></a>从定型检索任何其他 AutoML 模型

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>设置模型说明

Fitted_model 可以生成以下项目，这些项目将用于使用 automl_setup_model_explanations 获取工程和原始功能说明：

* 特征化定型示例/测试示例中的数据
* 收集工程和原始功能名称列表
* 在分类方案中查找标签列中的类

Automl_explainer_setup_obj 包含上述列表中的所有结构。

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>初始化说明的模拟功能重要性

有关 AutoML 模型的说明，请使用 `MimicWrapper` 类。 MimicWrapper 可以使用说明安装程序对象的参数、工作区和 LightGBM 模型进行初始化，该模型充当代理项模型以解释自动 ML 模型（此处为 fitted_model）。 MimicWrapper 还采用 automl_run 对象，在该对象中将上传原始和工程说明。

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

MimicWrapper 中的解释（）方法可与转换后的测试示例一起调用，以获取生成的工程功能的重要性。 你还可以使用 ExplanationDashboard，通过自动 ML featurizers 查看生成的工程功能的特征重要性值的虚线。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>使用模拟说明计算和可视化原始特征重要性

在转换后的测试示例中，可以再次调用 MimicWrapper 中的解释（）方法，并将 `get_raw` 设置为 True，以获取原始功能的特征重要性。 还可以使用 ExplanationDashboard 查看原始功能的特征重要性值的仪表板可视化。

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>推理过程中的 Interpretability

本部分介绍如何使用说明操作自动 ML 模型，该模型用于计算上一部分中的说明。

### <a name="register-the-model-and-the-scoring-explainer"></a>注册模型和评分说明

使用 `TreeScoringExplainer` 创建计分说明，该计分将用于计算推导时的原始和工程功能重要性值。 请注意，您需要用之前计算的 feature_map 初始化计分说明。 计分说明将使用 feature_map 来返回原始功能重要性。

在下面的代码中，保存计分说明，并向模型管理服务注册模型和评分说明。

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

接下来，为已部署的模型创建容器中所需的环境依赖项。

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
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>使用测试数据的推理

使用某些测试数据的推理来查看自动 ML 模型的预测值，并查看预测值和原始功能对预测值的重要性的工程特征重要性。

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

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>可视化以帮助发现数据中的模式以及定型时间的说明

你还可以在[Azure 机器学习 studio](https://ml.azure.com)中的工作区中直观显示功能重要性图表。 自动 ML 运行完成后，需要单击 "查看模型详细信息"，这会将你转到特定的运行。 从此处，单击 "解释" 选项卡，查看 "解释可视化效果" 仪表板。 

[![机器学习 Interpretability 体系结构](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 SDK 的其他区域中启用模型解释和功能重要性，请参阅 interpretability 上的[概念](how-to-machine-learning-interpretability.md)文章。