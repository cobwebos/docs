---
title: 如何部署模型使用自定义 Docker 映像
titleSuffix: Azure Machine Learning service
description: 了解如何部署 Azure 机器学习服务模型时使用的自定义 Docker 映像。 在部署为训练的模型时，主机映像、 web 服务器和运行服务所需的其他组件中创建 Docker 映像。 虽然 Azure 机器学习服务为您提供的默认映像，也可以使用你自己的映像。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: bd0e8099be5422d561541aeb8911c9a1610befcb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272761"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>部署模型使用自定义 Docker 映像

了解如何部署 Azure 机器学习服务的训练的模型时使用的自定义 Docker 映像。

在训练的模型部署到 web 服务或 IoT Edge 设备时，创建 Docker 映像。 此映像包含模型、 conda 环境和使用该模型所需的资产。 它还包含 web 服务器以处理传入的请求时部署为 web 服务，并使用 Azure IoT 中心所必需的组件。

Azure 机器学习服务提供了默认的 Docker 映像，因此无需担心如何创建一个。 此外可以使用为创建的自定义映像_基本映像_。 为部署创建映像时，基础映像用作的起始点。 它提供了基础操作系统和组件。 然后，部署过程将其他组件，如模型、 conda 环境和其他资产添加到再将其部署的映像。

通常情况下，当你想要控制组件版本或节省时间，在部署期间创建的自定义映像。 例如，你可能想要在特定版本的 Python、 Conda 或其他组件上实现标准化。 您可能想要安装软件所需的模型，其中安装过程需要很长时间。 创建基本映像时安装软件意味着无需安装每个部署。

> [!IMPORTANT]
> 部署模型，不能重写如 web 服务器的核心组件或 IoT Edge 的组件。 这些组件提供已知的经过测试且受 Microsoft 的工作环境。

> [!WARNING]
> Microsoft 可能无法再以帮助解决问题引起的自定义映像。 如果遇到问题，可能需要使用的默认图像或 Microsoft 提供了以查看问题是否特定于你的映像的映像之一。

本文档分为两个部分：

* 创建自定义映像：提供有关创建自定义映像和配置到 Azure 容器注册表使用 Azure CLI 和机器学习 CLI 的身份验证向管理员和开发运营的信息。
* 使用自定义映像：为数据科学家和开发运营/MLOps 提供有关部署从 Python SDK 或机器学习 CLI 训练的模型时使用的自定义映像的信息。

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作组。 有关详细信息，请参阅[创建工作区](setup-create-workspace.md)一文。
* Azure 机器学习 SDK。 有关详细信息，请参阅 Python SDK 一部分[创建工作区](setup-create-workspace.md#sdk)一文。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [适用于 Azure 机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。
* [Azure 容器注册表](/azure/container-registry)或其他可访问 internet 的 Docker 注册表。
* 本文档中的步骤假定你熟悉创建和使用__推理配置__对象作为模型部署的一部分。 有关详细信息，请参阅的"准备部署"部分[部署的位置和方式](how-to-deploy-and-where.md#prepare-to-deploy)。

## <a name="create-a-custom-image"></a>创建自定义映像

在本部分中的信息假定使用 Azure 容器注册表的目的用于存储 Docker 映像。 使用以下清单规划时创建 Azure 机器学习服务的自定义映像：

* 将使用为 Azure 机器学习服务工作区中或独立的 Azure 容器注册表创建 Azure 容器注册表？

    当使用映像存储在__工作区的容器注册表__，不需要对注册表进行身份验证。 身份验证处理由工作区。

    > [!TIP]
    > 你的工作区的容器注册表创建第一次训练或部署模型使用的工作区。 如果已创建新的工作区中，但不是受过培训或创建某一模型，无 Azure 容器注册表将存在的工作区。

    检索工作区的 Azure 容器注册表名称的信息，请参阅[获取容器注册表名称](#getname)本文的部分。

    当使用映像存储在__独立容器注册表__，将需要配置至少具有读取访问权限的服务主体。 然后向从注册表使用映像的任何人提供的服务主体 ID （用户名） 和密码。 例外情况是如果您使容器注册表可公开访问。

    创建专用 Azure 容器注册表的信息，请参阅[创建专用容器注册表](/azure/container-registry/container-registery-get-started-azure-cli)。

    有关使用服务主体的 Azure 容器注册表的信息，请参阅[使用服务主体的 Azure 容器注册表身份验证](/azure/container-registry/container-registry-auth-service-principal)。

* Azure 容器注册表和映像信息：映像名称提供给任何人都需要使用它。 例如，名为图像`myimage`，在名为注册表中存储`myregistry`，作为引用`myregistry.azurecr.io/myimage`时使用的模型部署的映像

* 图像要求：Azure 机器学习服务仅支持 Docker 映像提供了以下软件：

    * Ubuntu 16.04 或更高版本。
    * Conda 4.5。 # 或更高版本。
    * Python 3.5。 # 或 3.6。 #。

<a id="getname"></a>

### <a name="get-container-registry-information"></a>获取容器注册表信息

在本部分中，了解如何获取 Azure 机器学习服务工作区的 Azure 容器注册表的名称。

> [!TIP]
> 你的工作区的容器注册表创建第一次训练或部署模型使用的工作区。 如果已创建新的工作区中，但不是受过培训或创建某一模型，无 Azure 容器注册表将存在的工作区。

如果已训练或部署使用 Azure 机器学习服务模型，已为你的工作区创建容器注册表。 若要查找此容器注册表的名称，请使用以下步骤：

1. 打开一个新的 shell 或命令提示符并使用以下命令向你的 Azure 订阅进行身份验证：

    ```azurecli-interactive
    az login
    ```

    按照提示对订阅进行身份验证。

2. 使用以下命令列出工作区的容器注册表。 替换为`<myworkspace>`与你的 Azure 机器学习服务工作区名称。 替换为`<resourcegroup>`与包含你的工作区的 Azure 资源组：

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    返回的信息类似于下文：

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`值是你的工作区的 Azure 容器注册表的名称。

### <a name="build-a-custom-image"></a>生成自定义映像

本部分演练在 Azure 容器注册表中创建自定义 Docker 映像中的步骤。

1. 创建一个名为的新文本文件`Dockerfile`，并使用以下文本作为内容：

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 从 shell 或命令提示符下，使用以下 Azure 容器注册表进行身份验证。 替换为`<registry_name>`具有你想要将映像存储在容器注册表的名称：

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. 若要上传 Dockerfile，并生成它时，使用以下命令。 替换为`<registry_name>`具有你想要将映像存储在容器注册表的名称：

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    在生成过程中，信息是流式传输回命令行。 如果生成成功，你将收到类似于以下文本：

    ```text
    Run ID: cda was successful after 2m56s
    ```

构建使用 Azure 容器注册表的映像的详细信息，请参阅[生成并运行使用 Azure 容器注册表任务的容器映像](/docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli.md)

将现有的映像上传到 Azure 容器注册表的详细信息，请参阅[将第一个映像推送到专用 Docker 容器注册表](/azure/container-registry/container-registry-get-started-docker-cli.md)。

## <a name="use-a-custom-image"></a>使用自定义映像

若要使用自定义映像，需要以下信息：

* __映像名称__。 例如，`mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`是 Microsoft 提供的一个基本 Docker 映像的路径。
* 如果图像位于__专用存储库__，需要以下信息：

    * 注册表__地址__。 例如，`myregistry.azureecr.io`。
    * 服务主体__用户名__并__密码__对注册表具有读取访问权限。

    如果没有此信息，Azure 容器注册表包含映像需求助管理员。

### <a name="publicly-available-images"></a>公开提供的映像

Microsoft 提供多个 docker 映像上可公开访问存储库，可以在此部分中的步骤：

| Image | 描述 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure 机器学习服务的基本映像 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | 包含 ONNX 运行时。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | 包含 ONNX 运行时和 CUDA 组件。 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | 包含 ONNX 运行时和 TensorRT。 |

> [!TIP]
> 由于这些映像都是公开可用，您不需要使用它们时提供的地址、 用户名或密码。

> [!IMPORTANT]
> 使用 CUDA 或 TensorRT 的 Microsoft 映像必须仅使用 Microsoft Azure 服务上。

> [!TIP]
>__如果 Azure 机器学习计算上训练模型__，并使用__1.0.22 版本或更高版本__的 Azure 机器学习 SDK，在定型过程中创建映像。 若要查找此映像的名称，使用`run.properties["AzureML.DerivedImageName"]`。 下面的示例演示如何使用此映像：
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>与 Azure 机器学习配合使用的图像

若要使用自定义映像，将`base_image`的属性[推理配置对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)到图像的地址：

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

此格式适用于这两个可公开访问你的工作区和容器注册表的 Azure 容器注册表存储映像。 例如，以下代码使用由 Microsoft 提供的默认映像：

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

若要使用中的图像__专用容器注册表__不在你的工作区中，必须指定存储库的用户名和密码的地址：

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>使用机器学习 CLI 的图像

> [!IMPORTANT]
> 当前机器学习 CLI 可以使用映像从 Azure 容器注册表的工作区或可公开访问的存储库。 它不能使用映像中独立专用注册表。

在部署时使用机器学习 CLI 的模型，您提供推理配置文件引用自定义映像。 下面的 JSON 文档演示如何引用中的公共容器注册表的映像：

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

此文件用于`az ml model deploy`命令。 `--ic`参数用于指定推理配置文件。

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

使用机器学习 CLI 部署模型的详细信息，请参阅"模型注册、 配置和部署"部分[适用于 Azure 机器学习服务 CLI 扩展](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)一文。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[部署的位置和方式](how-to-deploy-and-where.md)。
* 了解如何[训练和部署机器学习模型使用 Azure 管道](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)。