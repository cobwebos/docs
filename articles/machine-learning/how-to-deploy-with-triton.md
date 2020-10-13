---
title: '利用 Triton (preview 提供高性能模型) '
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用 Triton 推理服务器部署模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 9a6e2de07921d05e123154f604c3d1b369b3b89d
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998753"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 推理服务器 (预览版的高性能服务)  

了解如何使用 [NVIDIA Triton 推理服务器](https://developer.nvidia.com/nvidia-triton-inference-server) 来改善用于模型推理的 web 服务的性能。

用于推理模型的一种方法是使用 web 服务。 例如，部署到 Azure Kubernetes 服务或 Azure 容器实例。 默认情况下，Azure 机器学习为 web 服务部署使用单线程 *通用* web 框架。

Triton 是 *针对推理进行优化*的框架。 它提供更好的 Gpu 利用率和更具成本效益的推理。 在服务器端，它对传入的请求进行批处理，并提交这些批处理以进行推理。 批处理更好地利用 GPU 资源，是 Triton 性能的关键部分。

> [!IMPORTANT]
> 当前在 __预览版__中，使用 Triton 进行 Azure 机器学习的部署。 预览版功能可能不会被客户支持覆盖。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> 本文档中的代码片段用于说明目的，可能不会显示完整的解决方案。 有关工作示例代码，请参阅 [Azure 机器学习中的 Triton 的端到端示例](https://aka.ms/aml-triton-sample)。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。
* 熟悉 [如何以及在何处使用 Azure 机器学习部署模型](how-to-deploy-and-where.md) 。
* [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) **、** [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)和[机器学习扩展](reference-azure-machine-learning-cli.md)。
* 用于本地测试的 Docker 的工作安装。 有关安装和验证 Docker 的信息，请参阅 docker 文档中的 [方向和设置](https://docs.docker.com/get-started/) 。

## <a name="architectural-overview"></a>体系结构概述

在尝试将 Triton 用于自己的模型之前，请务必了解它如何处理 Azure 机器学习以及它如何与默认部署进行比较。

**不带 Triton 的默认部署**

* 已启动多个 [Gunicorn](https://gunicorn.org/) 辅助角色来并发处理传入的请求。
* 这些工作人员处理预处理、调用模型和后期处理。 
* 推理请求使用 __计分 URI__。 例如，`https://myserevice.azureml.net/score` 。

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="正常，非 triton，部署体系结构图":::

**具有 Triton 的推理配置部署**

* 已启动多个 [Gunicorn](https://gunicorn.org/) 辅助角色来并发处理传入的请求。
* 请求会转发到 **Triton 服务器**。 
* Triton 按批处理请求，以最大程度地提高 GPU 利用率。
* 客户端使用 __评分 URI__ 发出请求。 例如，`https://myserevice.azureml.net/score` 。

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="正常，非 triton，部署体系结构图":::

为模型部署使用 Triton 的工作流为：

1. 验证 Triton 能否为你的模型提供服务。
1. 验证是否可以将请求发送到 Triton 部署的模型。
1. 将 Triton 特定的代码合并到 AML 部署中。

## <a name="optional-define-a-model-config-file"></a> (可选) 定义模型配置文件

模型配置文件告知 Triton 需要多少输入以及这些输入将是什么维度。 有关创建配置文件的详细信息，请参阅 NVIDIA 文档中的 " [模型配置](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) "。

> [!TIP]
> 在 `--strict-model-config=false` 启动 Triton 推理服务器时使用选项，这意味着无需为 `config.pbtxt` ONNX 或 TensorFlow 模型提供文件。
> 
> 有关此选项的详细信息，请参阅 NVIDIA 文档中的 [生成的模型配置](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) 。

## <a name="directory-structure"></a>目录结构

向 Azure 机器学习注册模型时，可以注册单独的文件或目录结构。 若要使用 Triton，模型注册必须针对包含名为的目录的目录结构 `triton` 。 此目录的一般结构为：

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> 此目录结构是一个 Triton 模型存储库，模型 (需要使用该结构) 才能使用 Triton。 有关详细信息，请参阅 NVIDIA 文档中的 [Triton 模型存储库](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) 。

## <a name="test-with-triton-and-docker"></a>用 Triton 和 Docker 测试

若要测试模型以确保它与 Triton 一起运行，可以使用 Docker。 以下命令将 Triton 容器提取到本地计算机，然后启动 Triton 服务器：

1. 若要将 Triton 服务器的映像提取到本地计算机，请使用以下命令：

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. 若要启动 Triton 服务器，请使用以下命令。 替换为 `<path-to-models/triton>` 包含模型的 Triton 模型存储库的路径：

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > 如果使用的是 Windows，则在首次运行命令时，系统可能会提示你允许网络连接到此进程。 如果是这样，请选择启用访问权限。

    启动后，将在命令行中记录类似于以下文本的信息：

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    第一行指示 web 服务的地址。 在此示例中，与 `0.0.0.0:8000` 相同 `localhost:8000` 。

1. 使用诸如卷之类的实用程序来访问运行状况终结点。

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    此命令返回类似于以下内容的信息。 请注意 `200 OK` ; 此状态表示 web 服务器正在运行。

    ```bash
    *   Trying 127.0.0.1:8000...
    * Connected to localhost (127.0.0.1) port 8000 (#0)
    > GET /v2/health/ready HTTP/1.1
    > Host: localhost:8000
    > User-Agent: curl/7.71.1
    > Accept: */*
    >
    * Mark bundle as not supporting multiuse
    < HTTP/1.1 200 OK
    HTTP/1.1 200 OK
    ```

除了基本运行状况检查以外，你还可以创建一个客户端，以便将数据发送到 Triton 进行推理。 有关创建客户端的详细信息，请参阅 NVIDIA 文档中的 [客户端示例](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) 。 [Triton GitHub 上](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)还提供了 Python 示例。

有关使用 Docker 运行 Triton 的详细信息，请参阅 [在具有 gpu 的系统上运行 Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) ，并在 [没有 gpu 的系统上运行 Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu)。

## <a name="register-your-model"></a> 注册模型

现在，你已验证你的模型适用于 Triton，请将其注册到 Azure 机器学习。 模型注册将模型文件存储在 Azure 机器学习工作区中，并在使用 Python SDK 和 Azure CLI 进行部署时使用。

下面的示例演示如何将模型注册 () ：

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>添加预处理和后处理

验证 web 服务是否正常工作后，可以通过定义 _条目脚本_来添加预处理代码和后处理代码。 此文件的名称为 `score.py`。 有关输入脚本的详细信息，请参阅 [定义条目脚本](how-to-deploy-and-where.md#define-an-entry-script)。

这两个主要步骤是在方法中初始化 Triton HTTP 客户端 `init()` ，并在函数中调用该客户端 `run()` 。

### <a name="initialize-the-triton-client"></a>初始化 Triton 客户端

在文件中包含类似于以下示例的代码 `score.py` 。 Azure 机器学习中的 Triton 预期会在 localhost，端口8000上解决。 在这种情况下，localhost 位于此部署的 Docker 映像中，而不是本地计算机上的端口：

> [!TIP]
> `tritonhttpclient`Pip 包包含在特选 `AzureML-Triton` 环境中，因此无需将其指定为 pip 依赖项。

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>修改评分脚本以调入 Triton

下面的示例演示如何动态请求模型的元数据：

> [!TIP]
> 你可以使用 Triton 客户端的方法，动态请求已通过 Triton 加载的模型的元数据 `.get_model_metadata` 。 有关其用法的示例，请参阅 [示例笔记本](https://aka.ms/triton-aml-sample) 。

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

## <a name="redeploy-with-an-inference-configuration"></a>使用推理配置重新部署

推理配置允许你使用条目脚本以及使用 Python SDK 或 Azure CLI Azure 机器学习部署过程。

> [!IMPORTANT]
> 必须指定 `AzureML-Triton` [特选环境](./resource-curated-environments.md)。
>
> Python 代码示例将克隆 `AzureML-Triton` 到另一个名为 `My-Triton` 的环境。 Azure CLI 代码还使用此环境。 有关克隆环境的详细信息，请参阅 [环境. Clone ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#clone-new-name-) 引用。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> 有关创建推理配置的详细信息，请参阅 [推理配置架构](./reference-azure-machine-learning-cli.md#inference-configuration-schema)。

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

部署完成后，将显示计分 URI。 对于这种本地部署，它将是 `http://localhost:6789/score` 。 如果要部署到云，可以使用 [az ml service show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI 命令获取计分 URI。

有关如何创建将推理请求发送到计分 URI 的客户端的信息，请参阅 [使用部署为 web 服务的模型](how-to-consume-web-service.md)。

## <a name="clean-up-resources"></a>清理资源

如果计划继续使用 "Azure 机器学习" 工作区，但想要删除已部署的服务，请使用以下选项之一：

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>后续步骤

* [请参阅 Azure 机器学习中 Triton 的端到端示例](https://aka.ms/aml-triton-sample)
* 查看 [Triton 客户端示例](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* 阅读 [Triton 推理服务器文档](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [排查部署失败的问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
