---
title: 将 ml 模型部署到 Azure Functions 的应用程序（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习将模型部署到 Azure Functions 应用程序。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 9fba3221656405f2bf2b1654b43d687f1915cca6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74542389"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>将机器学习模型部署到 Azure Functions （预览版）
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何在 Azure Functions 中将模型作为函数应用部署 Azure 机器学习。

> [!IMPORTANT]
> 尽管 Azure 机器学习和 Azure Functions 都已正式发布，但从适用于函数的机器学习服务中打包模型的功能处于预览阶段。

通过 Azure 机器学习，您可以从训练的机器学习模型创建 Docker 映像。 Azure 机器学习现在提供了预览功能，可将这些机器学习模型生成到可[部署到 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)中的函数应用。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区一](how-to-manage-workspace.md)文。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 已在工作区中注册的经过训练的机器学习模型。 如果没有模型，请使用[图像分类教程：训练模型](tutorial-train-models-with-aml.md)训练和注册。

    > [!IMPORTANT]
    > 本文中的代码片段假设您已设置了以下变量：
    >
    > * `ws`-Azure 机器学习工作区。
    > * `model`-将要部署的注册模型。
    > * `inference_config`-模型的推理配置。
    >
    > 有关设置这些变量的详细信息，请参阅[部署模型与 Azure 机器学习](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>准备部署

在部署之前，必须定义将模型作为 web 服务运行所需的内容。 以下列表描述了部署所需的基本项：

* 一个__项脚本__。 此脚本接受请求，使用模型为请求评分，并返回结果。

    > [!IMPORTANT]
    > 条目脚本特定于模型;它必须了解传入请求数据的格式、模型所需的数据格式，以及返回给客户端的数据的格式。
    >
    > 如果请求数据的格式不能由您的模型使用，则该脚本可以将其转换为可接受的格式。 它还可能在将响应返回给客户端之前对其进行转换。
    >
    > 默认情况下，在打包功能时，输入将被视为文本。 如果你有兴趣使用输入的原始字节（例如 Blob 触发器），则应使用[AMLRequest 来接受原始数据](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data)。


* 运行条目脚本或模型所需的**依赖项**（如帮助程序脚本或 Python/Conda 包）

这些实体封装为__推理配置__。 推理配置引用入口脚本和其他依赖项。

> [!IMPORTANT]
> 创建用于 Azure Functions 的推理配置时，必须使用[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)对象。 下面的示例演示如何创建环境对象并将其用于推理配置：
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

有关环境的详细信息，请参阅[创建和管理用于定型和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息，请参阅[部署具有 Azure 机器学习的模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到函数时，无需创建__部署配置__。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安装 SDK preview 包以提供函数支持

若要为 Azure Functions 生成包，必须安装 SDK 预览版包。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>创建映像

若要创建部署到 Azure Functions 的 Docker 映像，请使用所需的[contrib](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)或特定包函数作为你感兴趣的触发器。 下面的代码段演示如何使用模型和推理配置中的 blob 触发器创建新包：

> [!NOTE]
> 代码段假定 `model` 包含已注册的模型，并且 `inference_config` 包含推理环境的配置。 有关详细信息，请参阅[部署模型与 Azure 机器学习](how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

当 `show_output=True`时，将显示 Docker 生成过程的输出。 完成此过程后，将在工作区的 Azure 容器注册表中创建映像。 构建映像后，将显示 Azure 容器注册表中的位置。 返回的位置 `<acrinstance>.azurecr.io/package@sha256:<hash>`格式。

> [!NOTE]
> 函数的打包当前支持 HTTP 触发器、Blob 触发器和服务总线触发器。 有关触发器的详细信息，请参阅[Azure Functions 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns)。

> [!IMPORTANT]
> 保存位置信息，因为它在部署映像时使用。

## <a name="deploy-image-as-a-web-app"></a>将映像部署为 web 应用

1. 使用以下命令获取包含映像的 Azure 容器注册表的登录凭据。 将 `<acrinstance>` 替换为之前从 `package.location`返回的值： 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    此命令的输出类似于以下 JSON 文档：

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    保存__用户名__和__密码__之一的值。

1. 如果你还没有资源组或应用服务计划来部署服务，以下命令将演示如何创建这两项：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    在此示例中，将使用_Linux Premium_定价层（`--sku EP1`）。

    > [!IMPORTANT]
    > Azure 机器学习使用 Linux 创建的映像，因此必须使用 `--is-linux` 参数。

1. 若要创建 function app，请使用以下命令。 将 `<app-name>` 替换为要使用的名称。 将 `<acrinstance>` 和 `<imagename>` 替换为之前返回的 `package.location` 中的值：

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > 此时，已创建 function app。 但是，由于尚未向包含映像的 Azure 容器注册表提供 blob 触发器或凭据的连接字符串，因此该函数应用处于非活动状态。 在后续步骤中，你将为容器注册表提供连接字符串和身份验证信息。 

1. 创建要用作触发器的存储帐户，并获取它的连接字符串。

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    记录此连接字符串以提供给函数应用。 稍后我们将使用它来询问 `<triggerConnectionString>`

1. 在存储帐户中创建用于输入和输出的容器。 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 你将需要使用以下命令检索与创建的容器关联的标记：

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    显示的最近标记将在下面 `imagetag`。

1. 若要向函数应用提供访问容器注册表所需的凭据，请使用以下命令。 将 `<app-name>` 替换为要使用的名称。 将 `<acrinstance>` 和 `<imagetag>` 替换为上一步中 AZ CLI 调用中的值。 将 `<username>` 和 `<password>` 替换为之前检索到的 ACR 登录信息：

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    此命令返回类似于下面的 JSON 文档的信息：

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

此时，函数应用将开始加载映像。

> [!IMPORTANT]
> 加载映像可能需要几分钟时间。 可以使用 Azure 门户监视进度。

## <a name="next-steps"></a>后续步骤

* 了解如何在[函数](https://docs.microsoft.com/azure/azure-functions/functions-create-function-linux-custom-imag)文档中配置函数应用。
* 了解有关 Blob 存储的详细信息，请参阅[Azure blob 存储绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)。
* [将模型部署到 Azure App Service](how-to-deploy-app-service.md)。
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [API 参考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)