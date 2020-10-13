---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning
description: 了解部署 Azure 机器学习模型（包括 Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列）的方式和位置。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 09/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 40d8d4596e7d93b589e44f2dde2d1cb453566d71
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999235"
---
# <a name="deploy-models-with-azure-machine-learning"></a>使用 Azure 机器学习部署模型

了解如何将机器学习模型作为 Web 服务部署在 Azure 云或 Azure IoT Edge 设备中。

无论你在何处部署模型，工作流都是类似的：

1.  (可选中注册模型，请参阅下面) 。
1. 准备推理配置 (除非使用 [无代码部署](./how-to-deploy-no-code-deployment.md)) 。
1. 准备条目脚本 (，除非使用 [无代码部署](./how-to-deploy-no-code-deployment.md)) 。
1. 选择计算目标。
1. 将模型部署到计算目标。
1. 测试生成的 web 服务。

有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习管理、部署和监视模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先决条件

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [机器学习服务的 Azure 命令行界面 (CLI) 扩展](reference-azure-machine-learning-cli.md)。

# <a name="python"></a>[Python](#tab/python)

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [适用于 Python 的 Azure 机器学习软件开发工具包 (SDK)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)。

---

## <a name="connect-to-your-workspace"></a>连接到工作区

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

按照 Azure CLI 文档中的说明[设置订阅上下文](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)。

然后执行以下命令：

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

以便查看有权访问的工作区。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

若要详细了解如何使用 SDK 连接到工作区，请参阅[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#&preserve-view=trueworkspace) 文档。


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> (可选) 注册模型

已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如，如果有一个存储在多个文件中的模型，则可以在工作区中将这些文件注册为单个模型。 注册这些文件后，可以下载或部署已注册的模型，并接收注册的所有文件。

> [!TIP] 
> 建议为版本跟踪注册模型，但不是必需的。 如果要在不注册模型的情况下继续操作，则需要在 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 中指定一个源目录或 [ 在上inferenceconfig.js](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ，并确保您的模型位于该源目录内。

> [!TIP]
> 注册模型时，请提供云位置（来自训练运行）或本地目录的路径。 此路径仅用于在注册过程中查找要上传的文件。 它不需要与入口脚本中使用的路径匹配。 有关详细信息，请参阅[在入口脚本中查找模型文件](./how-to-deploy-advanced-entry-script.md#load-registered-models)。

以下示例演示如何注册模型。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>通过 Azure ML 训练运行注册一个模型

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `--asset-path` 设置为包含文件的文件夹的路径。

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

若要在模型注册中包含多个文件，请将 `-p` 设置为包含文件的文件夹的路径。

有关 `az ml model register` 的详细信息，请参阅[参考文档](/cli/azure/ext/azure-cli-ml/ml/model)。

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>通过 Azure ML 训练运行注册一个模型

  使用 SDK 训练模型时，可以接收 [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) 对象或 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 对象，具体取决于模型的训练方式。 每个对象都可用于注册通过实验运行创建的模型。

  + 通过 `azureml.core.Run` 对象注册模型：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。 有关详细信息，请参阅 [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 文档。

  + 通过 `azureml.train.automl.run.AutoMLRun` 对象注册模型：

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    在此示例中，未指定 `metric` 和 `iteration` 参数，因此将注册具有最佳主要指标的迭代。 不会使用模型名称，而是使用从运行返回的 `model_id` 值。

    有关详细信息，请参阅 [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 文档。

### <a name="register-a-model-from-a-local-file"></a>通过本地文件注册模型

可以通过提供模型的本地路径来注册模型。 可以提供文件夹或单个文件的路径。 可以使用此方法来注册使用 Azure 机器学习训练并下载的模型。 也可以使用此方法来注册在 Azure 机器学习之外训练的模型。

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **使用 SDK 和 ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。

有关详细信息，请参阅关于[模型类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true)的文档。

若要详细了解如何使用在 Azure 机器学习之外训练的模型，请参阅[如何部署现有模型](how-to-deploy-existing-model.md)。

---

## <a name="define-an-entry-script"></a>定义入口脚本

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>定义推理配置


推理配置描述如何设置包含模型的 Web 服务。 此配置稍后在部署模型时使用。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

最小推理配置可以编写为：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

这指定部署将使用目录中的文件 `score.py` `./working_dir` 来处理传入的请求。

[请参阅此文](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ，了解有关推理配置的更详尽讨论。 

# <a name="python"></a>[Python](#tab/python)

下面的示例演示：

1. 从工作区加载[特选环境](resource-curated-environments.md)
1. 克隆环境
1. 指定 `scikit-learn` 为依赖项。
1. 使用环境创建 InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息，请参阅 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 类文档。

---

> [!TIP] 
> 若要详细了解如何将自定义 Docker 映像与推理配置结合使用，请参阅[如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

## <a name="choose-a-compute-target"></a>选择计算目标

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>定义部署配置

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

适用于部署配置的选项因所选计算目标而异。

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

有关详细信息，请参阅 [此参考](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)。

# <a name="python"></a>[Python](#tab/python)

在部署模型之前，必须定义部署配置。 部署配置特定于将托管 Web 服务的计算目标**。 例如，在本地部署模型时，必须指定服务接受请求的端口。 该部署配置不属于入口脚本。 它用于定义将托管模型和入口脚本的计算目标的特征。

例如，如果没有与工作区关联的 Azure Kubernetes 服务 (AKS) 实例，则可能还需要创建计算资源。

下表提供了为每个计算目标创建部署配置的示例：

| 计算目标 | 部署配置示例 |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器实例 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes 服务 | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

可从 `azureml.core.webservice` 导入的本地、Azure 容器实例和 AKS Web 服务的类：

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-model"></a>部署模型

现在已准备好部署模型。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>使用已注册的模型

如果在 Azure 机器学习工作区中注册了模型，请将“mymodel:1”替换为模型的名称及其版本号。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>使用本地模型

如果不想注册模型，则可在 inferenceconfig.json 中传递 "sourceDirectory" 参数，以指定用于提供模型的本地目录。

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示了本地部署。 语法因你在上一步选择的计算目标而异。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关详细信息，请参阅关于 [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true)[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py&preserve-view=true) 的文档。

---

### <a name="understanding-service-state"></a>了解服务状态

在模型部署期间，当模型完全部署时，你可能会看到服务状态发生更改。

下表描述了各种服务状态：

| Webservice 状态 | 说明 | 最终状态？
| ----- | ----- | ----- |
| 正在转换 | 此服务正在进行部署。 | 否 |
| 不正常 | 此服务已部署，但当前无法访问。  | 否 |
| 不可安排 | 由于缺少资源，此时无法部署此服务。 | 否 |
| 已失败 | 由于出现错误或崩溃，服务未能部署。 | 是 |
| 正常 | 服务正常，终结点可用。 | 是 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> 批量推理
Azure 机器学习计算目标由 Azure 机器学习创建和管理。 它们可用于 Azure 机器学习管道中的批量预测。

若要查看使用 Azure 机器学习计算进行批量推理的演练，请参阅[如何运行批量预测](tutorial-pipeline-batch-scoring-classification.md)。

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge 推理
对部署到边缘的支持处于预览阶段。 有关详细信息，请参阅[将 Azure 机器学习部署为 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)。

## <a name="delete-resources"></a>删除资源

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

若要删除已部署的 webservice，请使用 `az ml service <name of webservice>`。

若要从工作区中删除已注册的模型，请使用 `az ml model delete <model id>`

详细了解如何[删除 webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) 和[删除模型](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)。

# <a name="python"></a>[Python](#tab/python)

若要删除已部署的 Web 服务，请使用 `service.delete()`。
若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息，请参阅关于 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) 和 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) 的文档。

---


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

