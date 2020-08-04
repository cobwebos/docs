---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542754"
---
## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](../articles/machine-learning/how-to-manage-workspace.md)。
- 模型。 如果没有已训练的模型，则可以使用[此教程](https://aka.ms/azml-deploy-cloud)中提供的模型和依赖项文件。
- [适用于 Python 的 Azure 机器学习软件开发工具包（SDK）](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>连接到工作区

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

若要详细了解如何使用 SDK 连接到工作区，请参阅[用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 文档。

## <a name="register-your-model"></a><a id="registermodel"></a> 注册模型

已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如，如果有一个存储在多个文件中的模型，则可以在工作区中将这些文件注册为单个模型。 注册这些文件后，可以下载或部署已注册的模型，并接收注册的所有文件。

> [!TIP]
> 注册模型时，请提供云位置（来自训练运行）或本地目录的路径。 此路径仅用于在注册过程中查找要上传的文件。 它不需要与入口脚本中使用的路径匹配。 有关详细信息，请参阅[在入口脚本中查找模型文件](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)。

机器学习模型会注册到 Azure 机器学习工作区中。 模型可以来自 Azure 机器学习或其他位置。 注册模型时，可以选择提供有关模型的元数据。 然后，可将应用于模型注册的 `tags` 和 `properties` 字典用于筛选模型。

以下示例演示如何注册模型。

### <a name="register-a-model-from-an-azure-ml-training-run"></a>从 Azure ML 定型运行注册模型

  使用 SDK 训练模型时，可以接收 [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 对象或 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 对象，具体取决于模型的训练方式。 每个对象都可用于注册通过实验运行创建的模型。

  + 通过 `azureml.core.Run` 对象注册模型：
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path` 参数表示模型的云位置。 本示例使用的是单个文件的路径。 若要在模型注册中包含多个文件，请将 `model_path` 设置为包含文件的文件夹的路径。 有关详细信息，请参阅 [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 文档。

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

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

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

有关详细信息，请参阅关于[模型类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的文档。

若要详细了解如何使用在 Azure 机器学习之外训练的模型，请参阅[如何部署现有模型](../articles/machine-learning/how-to-deploy-existing-model.md)。


## <a name="define-an-entry-script"></a>定义条目脚本

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>定义推理配置

推理配置描述如何设置包含模型的 Web 服务。 此配置稍后在部署模型时使用。

推理配置使用 Azure 机器学习环境来定义部署所需的软件依赖项。 利用环境，你可以创建、管理和重复使用训练和部署所需的软件依赖项。 可以从自定义依赖项文件创建环境，或使用特选 Azure 机器学习环境之一。 以下 YAML 是用于推理的 Conda 依赖项文件的一个示例。 请注意，必须将版本为 1.0.45 或更高版本的 azureml-defaults 指示为 pip 依赖项，因为它包含将模型托管为 Web 服务所需的功能。 如果要使用自动生成架构功能，则入口脚本也必须导入 `inference-schema` 包。

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> 如果你的依赖项可通过 Conda 和 pip（通过 PyPi）使用，Microsoft 建议使用 Conda 版本，因为 Conda 包通常附带预生成的二进制文件，能让安装更可靠。
>
> 有关详细信息，请参阅[了解 Conda 和 Pip](https://www.anaconda.com/understanding-conda-and-pip/)。
>
> 若要检查是否能通过 Conda 使用依赖项，请使用 `conda search <package-name>` 命令，或使用 [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) 和 [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) 处的包索引。

可以使用依赖项文件创建环境对象，并将其保存到工作区供将来使用：

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

有关使用 Azure 机器学习并自定义 Python 环境的详细讨论，请参阅[在 Azure 机器学习中创建 & 使用软件环境](../articles/machine-learning/how-to-use-environments.md)

若要详细了解如何将自定义 Docker 映像与推理配置结合使用，请参阅[如何使用自定义 Docker 映像部署模型](../articles/machine-learning/how-to-deploy-custom-docker-image.md)。


下面的示例演示如何从工作区加载环境，并将其与推理配置结合使用：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](../articles/machine-learning/how-to-use-environments.md)。

有关推理配置的详细信息，请参阅 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 类文档。

## <a name="choose-a-compute-target"></a>选择计算目标


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>定义部署配置

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


## <a name="deploy-your-model"></a>部署模型

你现在已准备好部署模型。 下面的示例演示了本地部署。 根据你在上一步中选择的计算目标，语法将有所不同。

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关详细信息，请参阅关于 [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)[Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 的文档。


## <a name="delete-resources"></a>删除资源

若要删除已部署的 Web 服务，请使用 `service.delete()`。
若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息，请参阅关于 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) 和 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) 的文档。

