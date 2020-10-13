---
title: 分析模型内存和 CPU 使用率
titleSuffix: Azure Machine Learning
description: 了解如何分析模型内存和 CPU 使用率
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 95f35c2734e0a8dbc39d409847976b8b31716f81
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998771"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>分析模型，确定资源利用率

本文介绍如何分析机器学习模型，以确定在将模型部署为 Web 服务时需要为该模型分配多少 CPU 和内存。

## <a name="prerequisites"></a>必备条件

本文假设你已通过 Azure 机器学习训练并注册了一个模型。 有关使用 Azure 机器学习训练和注册 scikit-learn 模型的示例，请参阅[此处的示例教程](how-to-train-scikit-learn.md)。

## <a name="limitations"></a>限制

* 当工作区的 Azure 容器注册表 (ACR) 位于虚拟网络后面时，分析将不起作用。

## <a name="run-the-profiler"></a>运行探查器

注册模型并准备好部署所需的其他组件后，即可确定部署的服务将需要的 CPU 和内存。 分析可测试运行模型并返回 CPU 使用情况、内存使用情况和响应延迟等信息的服务。 它还根据资源使用情况提供 CPU 和内存建议。

若要分析模型，你需要：
* 已注册的模型。
* 基于入口脚本和推理环境定义的推理配置。
* 单列表格数据集，其中的每行都包含一个表示示例请求数据的字符串。

> [!IMPORTANT]
> 目前，我们仅支持分析预期其请求数据为字符串的服务，例如：字符串序列化 json、文本、字符串序列化图像等。数据集的每一行的内容（字符串）都会放入 HTTP 请求的正文中，然后会被发送到可以对评分模型进行封装的服务。

> [!IMPORTANT]
> 我们仅支持对 ChinaEast2 和 USGovArizona 区域中的2个 Cpu 进行分析。

下面是一个示例，说明了如何构造用于分析服务的输入数据集，该服务预期其传入请求数据包含序列化 json。 在此示例中，我们创建了一个数据集，该数据集基于 100 个请求数据内容相同的实例。 在实际方案中，建议使用包含各种输入的更大数据集，尤其是在模型资源使用/行为是依赖于输入的情况下。

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

准备好包含示例请求数据的数据集后，即可创建推理配置。 推理配置基于 score.py 和环境定义。 以下示例演示如何创建推理配置和运行分析：

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


以下命令演示如何使用 CLI 分析模型：

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> 要保留分析步骤所返回的信息，请为模型使用标记或属性。 使用标记或属性在模型注册表中存储模型数据。 以下示例演示添加包含 `requestedCpu` 和 `requestedMemoryInGb` 信息的新标记：
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>后续步骤

* [排查部署失败的问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)

