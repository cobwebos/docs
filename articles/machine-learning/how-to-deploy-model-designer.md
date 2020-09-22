---
title: 使用 studio 部署在设计器中训练的模型
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习 studio 部署在设计器中训练的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 09/04/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 95b41723d3cb398caad3a0cf388b7810deda78dc
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933609"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>使用 studio 部署在设计器中训练的模型

本文介绍如何在 Azure 机器学习 studio 中将定型模型作为实时终结点部署到设计器中。

Studio 中的部署包括以下步骤：

1. 注册定型模型。
1. 下载模型的条目脚本和 conda 依赖项文件。
1. 将模型部署到计算目标。

你还可以直接在设计器中部署模型，以便跳过模型注册和文件下载步骤。 这对于快速部署非常有用。 有关详细信息，请参阅 [使用设计器部署模型](tutorial-designer-automobile-price-deploy.md)。

还可以通过 SDK 或命令行界面 (CLI) 部署设计器训练的模型。 有关详细信息，请参阅 [部署具有 Azure 机器学习的现有模型](how-to-deploy-existing-model.md)。

## <a name="prerequisites"></a>先决条件

* [一个 Azure 机器学习工作区](how-to-manage-workspace.md)

* 包含[定型模型模块](./algorithm-module-reference/train-model.md)的已完成训练管道

## <a name="register-the-model"></a>注册模型

训练管道完成后，将训练的模型注册到 Azure 机器学习工作区，以访问其他项目中的模型。

1. 选择 " [训练模型" 模块](./algorithm-module-reference/train-model.md)。
1. 在右窗格中选择 " **输出 + 日志** " 选项卡。
1. 选择齿轮图标的 " **注册模型** " 图标 ![ 屏幕截图 ](./media/how-to-deploy-model-designer/register-model-icon.png) 。

    !["定型模型" 模块的右窗格屏幕截图](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. 输入模型的名称，并选择 " **保存**"。

注册模型后，可以在工作室的 " **模型** 资产" 页中找到它。
    
!["模型资产" 页中已注册模型的屏幕截图](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>下载条目脚本文件和 conda 依赖项文件

若要在 Azure 机器学习 studio 中部署模型，需要以下文件：

- **条目脚本文件** -加载训练的模型，处理来自请求的输入数据，执行实时推断，并返回结果。 "定型模型" 模块完成后，设计器将自动生成一个 `score.py` 条目脚本文件。 **Train Model**

- **Conda 依赖项文件** -指定你的 webservice 依赖哪些 pip 和 Conda 包。 " `conda_env.yaml` **定型模型** " 模块完成后，设计器会自动创建一个文件。

您可以在 " **训练模型** " 模块的右窗格中下载这两个文件：

1. 选择**训练模型**模块。
1. 在 " **输出 + 日志** " 选项卡中，选择文件夹 `trained_model_outputs` 。
1. 下载 `conda_env.yaml` 文件和 `score.py` 文件。

    ![用于在右窗格中进行部署的下载文件的屏幕截图](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

或者，在注册模型后，你可以从 " **模型** 资产" 页下载文件：

1. 导航到 " **模型** 资产" 页。
1. 选择要部署的模型。
1. 选择 " **项目** " 选项卡。
1. 选择 `trained_model_outputs` 文件夹。
1. 下载 `conda_env.yaml` 文件和 `score.py` 文件。  

    ![模型详细信息页中用于部署的下载文件的屏幕截图](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> 该 `score.py` 文件提供与 **评分模型** 模块几乎相同的功能。 不过，一些模块（如 [分数 SVD 推荐器](./algorithm-module-reference/score-svd-recommender.md)、 [分数范围和深度推荐器](./algorithm-module-reference/score-wide-and-deep-recommender.md)）和 [评分 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 都具有用于不同计分模式的参数。 你还可以在条目脚本中更改这些参数。
>
>有关在文件中设置参数的详细信息 `score.py` ，请参阅 [配置条目脚本](#configure-the-entry-script)部分。

## <a name="deploy-the-model"></a>部署模型

下载所需的文件后，就可以部署该模型了。

1. 在 " **模型** 资产" 页中，选择已注册的模型。
1. 选择 " **部署** " 按钮。
1. 在 "配置" 菜单中，输入以下信息：

    - 输入终结点的名称。
    - 选择此可将模型部署到 [Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md) 或 [azure 容器实例](how-to-deploy-azure-container-instance.md)。
    - `score.py`为**输入脚本文件**上传。
    - `conda_env.yml`为**Conda 依赖项文件**上传。 

    >[!TIP]
    > 在 " **高级** " 设置中，你可以为部署设置 CPU/内存容量和其他参数。 这些设置对于某些模型（如 PyTorch 模型）非常重要，这会占用大量的 memery (大约 4 GB) 。

1. 选择 " **部署** "，将模型部署为实时终结点。

    ![在模型资产页中部署模型的屏幕截图](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>使用实时终结点

部署成功后，可以在 " **终结点** 资产" 页中找到实时终结点。 在此之后，你会发现一个 REST 终结点，客户端可以使用该终结点将请求提交到实时终结点。 

> [!NOTE]
> 设计器还会生成一个用于测试的示例数据 json 文件，您可以 `_samples.json` 在 **trained_model_outputs** 文件夹中下载该文件。

使用以下代码示例来使用实时终结点。

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

## <a name="configure-the-entry-script"></a>配置条目脚本

设计器中的一些模块（如 [评分 SVD 推荐器](./algorithm-module-reference/score-svd-recommender.md)、 [分数宽和深度推荐器](./algorithm-module-reference/score-wide-and-deep-recommender.md)）和 [评分 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 都具有用于不同计分模式的参数。 本部分介绍如何在条目脚本文件中更新这些参数。

下面的示例将更新训练的 **宽 & Deep 推荐器** 模型的默认行为。 默认情况下，该 `score.py` 文件会告知 web 服务预测用户和项之间的评级。 

您可以通过更改参数来修改条目脚本文件以生成项目建议并返回推荐的项目 `recommender_prediction_kind` 。


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

对于 **Wide & Deep 推荐器** 和 **Vowpal Wabbit** 模型，可以使用以下方法配置评分模式参数：

- 参数名称是 [计分 Vowpal Wabbit 模型](./algorithm-module-reference/score-vowpal-wabbit-model.md) 和 [分数宽和深度推荐器](./algorithm-module-reference/score-wide-and-deep-recommender.md)的参数名称的小写和下划线组合;
- 模式类型参数值是相应选项名称的字符串。 在上述代码中采用 **推荐器预测类型** ，例如，值可以是 `'Rating Prediction'` 或 `'Item Recommendation'` 。 不允许使用其他值。

对于 **SVD 推荐器** 训练的模型，参数名称和值可能不太明显，您可以查找下面的表格以决定如何设置参数。

| [分数 SVD 推荐器](./algorithm-module-reference/score-svd-recommender.md)中的参数名称                           | 输入脚本文件中的参数名称 |
| ------------------------------------------------------------ | --------------------------------------- |
| 推荐器预测类型                                  | prediction_kind                         |
| 推荐的项选择内容                                   | recommended_item_selection              |
| 单个用户的建议池的最小大小    | min_recommendation_pool_size            |
| 要推荐给用户的最大项目数               | max_recommended_item_count              |
| 是否返回项目的预测分级以及标签 | return_ratings                          |

下面的代码演示如何设置 SVD 推荐器的参数，该参数使用全部六个参数来推荐附加了预测分级的分级项目。

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>后续步骤

* [在设计器中训练模型](tutorial-designer-automobile-price-train-score.md)
* [排查部署失败的问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
