---
title: 使用自定义 Docker 基本映像部署模型
titleSuffix: Azure Machine Learning
description: 了解如何在部署 Azure 机器学习模型时使用自定义 Docker 基本映像。 部署定型模型时，会部署一个基容器映像，以运行模型进行推理。 虽然 Azure 机器学习提供了一个默认的基本映像，但你也可以使用自己的基本映像。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/22/2019
ms.openlocfilehash: a9992fbaa20b0137fd503992fc9efba11c2dfd3b
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213490"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>使用自定义 Docker 基本映像部署模型

了解如何在使用 Azure 机器学习部署定型模型时使用自定义 Docker 基本映像。

将定型模型部署到 web 服务或 IoT Edge 设备时，将创建一个包含用于处理传入请求的 web 服务器的包。

Azure 机器学习提供了一个默认 Docker 基本映像，因此你无需担心如何创建它。 你还可以使用 Azure 机器学习__环境__选择特定的基本映像，或使用你提供的自定义映像。

为部署创建映像时，将使用基本映像作为起始点。 它提供基本的操作系统和组件。 然后，部署过程会在部署之前将其他组件（如模型、conda 环境和其他资产）添加到映像。

通常，当你想要使用 Docker 来管理依赖项时，请创建一个自定义的基本映像，维护组件版本的更紧密控制，或在部署期间节省时间。 例如，你可能希望在特定版本的 Python、Conda 或其他组件上实现标准化。 你可能还希望安装模型所需的软件，安装过程需要较长时间。 在创建基础映像时安装软件意味着无需为每个部署安装软件。

> [!IMPORTANT]
> 部署模型时，不能重写核心组件（如 web 服务器或 IoT Edge 组件）。 这些组件提供了由 Microsoft 测试和支持的已知的工作环境。

> [!WARNING]
> Microsoft 可能无法帮助解决自定义映像导致的问题。 如果遇到问题，系统可能会要求你使用 Microsoft 提供的默认图像或一个映像，以查看问题是否特定于你的映像。

本文档分为两部分：

* 创建自定义基本映像：提供有关使用 Azure CLI 和机器学习 CLI 创建自定义映像和配置 Azure 容器注册表身份验证的管理员和 DevOps 的信息。
* 使用自定义基本映像部署模型：向数据科学家和 DevOps/ML 工程师提供有关在从 Python SDK 或 ML CLI 部署定型模型时使用自定义映像的信息。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作组。 有关详细信息，请参阅[创建工作区一](how-to-manage-workspace.md)文。
* [AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 可在 internet 上访问的[Azure 容器注册表](/azure/container-registry)或其他 Docker 注册表。
* 本文档中的步骤假定你熟悉如何创建和使用__推理配置__对象作为模型部署的一部分。 有关详细信息，请参阅[部署位置和方式](how-to-deploy-and-where.md#prepare-to-deploy)的 "准备部署" 一节。

## <a name="create-a-custom-base-image"></a>创建自定义基本映像

本部分中的信息假定你使用 Azure 容器注册表来存储 Docker 映像。 计划为 Azure 机器学习创建自定义映像时，请使用以下清单：

* 是否将使用为 Azure 机器学习工作区或独立 Azure 容器注册表创建的 Azure 容器注册表？

    使用__工作区的容器注册表__中存储的图像时，无需对注册表进行身份验证。 身份验证由工作区处理。

    > [!WARNING]
    > __第一次使用工作区训练或部署模型__时，将创建工作区的 Azure 容器 Rzegistry。 如果已创建新的工作区，但未定型或创建了模型，则工作区不会存在任何 Azure 容器注册表。

    有关检索工作区的 Azure 容器注册表名称的信息，请参阅本文的[获取容器注册表名称](#getname)部分。

    使用存储在__独立容器注册表__中的映像时，需要配置至少具有读取访问权限的服务主体。 然后，将服务主体 ID （用户名）和密码提供给使用注册表中的映像的任何人。 例外情况是使容器注册表可公开访问。

    有关创建私有 Azure 容器注册表的信息，请参阅[创建专用容器注册表](/azure/container-registry/container-registry-get-started-azure-cli)。

    有关将服务主体用于 Azure 容器注册表的信息，请参阅[使用服务主体的 Azure 容器注册表身份验证](/azure/container-registry/container-registry-auth-service-principal)。

* Azure 容器注册表和映像信息：为需要使用该名称的任何人提供映像名称。 例如，在名`myimage` `myregistry`为的注册表中存储的名为的映像在使用映像进行模型部署时被称为`myregistry.azurecr.io/myimage`

* 映像要求：Azure 机器学习仅支持提供以下软件的 Docker 映像：

    * Ubuntu 16.04 或更高版本。
    * Conda 4.5. # 或更高版本。
    * Python 3.5. # 或 3.6. #。

<a id="getname"></a>

### <a name="get-container-registry-information"></a>获取容器注册表信息

本部分介绍如何获取 Azure 机器学习工作区的 Azure 容器注册表的名称。

> [!WARNING]
> __第一次使用工作区训练或部署模型__时，将创建工作区的 Azure 容器注册表。 如果已创建新的工作区，但未定型或创建了模型，则工作区不会存在任何 Azure 容器注册表。

如果已使用 Azure 机器学习训练或部署了模型，则会为工作区创建容器注册表。 若要查找此容器注册表的名称，请使用以下步骤：

1. 打开新的 shell 或命令提示符，然后使用以下命令对 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```

    按照提示对订阅进行身份验证。

2. 使用以下命令列出工作区的容器注册表。 将`<myworkspace>`替换为 Azure 机器学习工作区名称。 将`<resourcegroup>`替换为包含工作区的 Azure 资源组:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    返回的信息类似于下文：

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`该值是工作区的 Azure 容器注册表的名称。

### <a name="build-a-custom-base-image"></a>构建自定义基本映像

本部分中的步骤介绍如何在 Azure 容器注册表中创建自定义 Docker 映像。

1. 创建一个名为`Dockerfile`的新文本文件，并使用以下文本作为内容：

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 在 shell 或命令提示符下，使用以下命令对 Azure 容器注册表进行身份验证。 将`<registry_name>`替换为要在其中存储映像的容器注册表的名称：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上传 Dockerfile 并生成它，请使用以下命令。 将`<registry_name>`替换为要在其中存储映像的容器注册表的名称：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    在生成过程中，会将信息流式传输回命令行。 如果生成成功，则会收到类似于以下文本的消息：

    ```text
    Run ID: cda was successful after 2m56s
    ```

有关使用 Azure 容器注册表生成映像的详细信息，请参阅[使用 Azure 容器注册表任务构建并运行容器映像](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

有关将现有映像上传到 Azure 容器注册表的详细信息，请参阅将[第一个映像推送到专用 Docker 容器注册表](/azure/container-registry/container-registry-get-started-docker-cli)。

## <a name="use-a-custom-base-image"></a>使用自定义基本映像

若要使用自定义映像，需要以下信息：

* __映像名称__。 例如， `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`是 Microsoft 提供的基本 Docker 映像的路径。
* 如果映像在__专用存储库__中，则需要以下信息：

    * 注册表__地址__。 例如， `myregistry.azureecr.io` 。
    * 具有注册表读取访问权限的服务主体__用户名__和__密码__。

    如果没有此信息，请向管理员咨询包含映像的 Azure 容器注册表。

### <a name="publicly-available-base-images"></a>公开发布的基本映像

Microsoft 在可公开访问的存储库上提供了几个 docker 映像，可用于本部分中的步骤：

| 图像 | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure 机器学习的基本映像 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | 包含 ONNX 运行时。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | 包含 ONNX 运行时和 CUDA 组件。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | 包含 ONNX 运行时和 TensorRT。 |

> [!TIP]
> 由于这些映像是公开提供的，因此在使用这些映像时，无需提供地址、用户名或密码。

> [!IMPORTANT]
> 只有使用 CUDA 或 TensorRT 的 Microsoft 映像才能 Microsoft Azure 服务上使用。

有关详细信息，请参阅[Azure 机器学习容器](https://github.com/Azure/AzureML-Containers)。

> [!TIP]
>__如果您的模型在 Azure 机器学习计算上定型__，则使用 Azure 机器学习 SDK 的__版本1.0.22 或更高版本__，则在训练过程中将创建一个图像。 若要发现此映像的名称，请`run.properties["AzureML.DerivedImageName"]`使用。 下面的示例演示如何使用此映像：
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>将映像与 Azure 机器学习 SDK 一起使用

若要使用存储在**工作区的 Azure 容器注册表**中的映像，或者使用可**公开访问的容器注册表**，请设置以下[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)属性：

+ `docker.enabled=True`
+ `docker.base_image`：设置为注册表，并将路径设置为图像的路径。

```python
from azureml.core import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

若要使用不在工作区中的__专用容器注册表__中的映像，则必须使用`docker.base_image_registry`指定存储库的地址以及用户名和密码：

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"
```

定义环境后，将其与[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)对象一起使用，以定义模型和 web 服务将在其中运行的推理环境。

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

此时，你可以继续进行部署。 例如，以下代码片段将使用推理配置和自定义映像在本地部署 web 服务：

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关部署的详细信息，请参阅[部署具有 Azure 机器学习的模型](how-to-deploy-and-where.md)。

### <a name="use-an-image-with-the-machine-learning-cli"></a>使用带有机器学习 CLI 的映像

> [!IMPORTANT]
> 目前机器学习 CLI 可以使用工作区的 Azure 容器注册表中的映像或可公开访问的存储库。 它不能使用独立的专用注册表中的映像。

使用机器学习 CLI 部署模型时，需要提供引用自定义映像的推理配置文件。 下面的 JSON 文档演示如何在公共容器注册表中引用图像：

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

此文件与`az ml model deploy`命令一起使用。 `--ic`参数用于指定推理配置文件。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

有关使用 ML CLI 部署模型的详细信息，请参阅 Azure 机器学习文章的[CLI 扩展](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)的 "模型注册、分析和部署" 部分。

## <a name="next-steps"></a>后续步骤

* 详细了解[部署位置和方式](how-to-deploy-and-where.md)。
* 了解如何[使用 Azure Pipelines 训练和部署机器学习模型](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)。
