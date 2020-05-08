---
title: 将 ml 模型部署到 Azure Functions 应用（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习将模型部署到 Azure Functions 应用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: 104e0892e2ad6bc6a0b3212722781f9498eee219
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82745001"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>将机器学习模型部署到 Azure Functions（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何将 Azure 机器学习中的模型部署为 Azure Functions 中的函数应用。

> [!IMPORTANT]
> 虽然 Azure 机器学习和 Azure Functions 都已正式发布，但将模型从机器学习服务部署到 Functions 的功能目前处于预览阶段。

使用 Azure 机器学习，可通过经过训练的机器学习模型创建 Docker 映像。 Azure 机器学习现在提供了将这些机器学习模型生成到函数应用中的预览功能，而这些函数应用可[部署到 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container) 中。

## <a name="prerequisites"></a>必备条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)一文。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 工作区中注册的已训练的机器学习模型。 如果没有模型，请使用[图像分类教程：训练模型](tutorial-train-models-with-aml.md)来训练和注册模型。

    > [!IMPORTANT]
    > 本文中的代码片段假设你已设置以下变量：
    >
    > * `ws` - Azure 机器学习工作区。
    > * `model` - 将要部署的注册模型。
    > * `inference_config` - 用于模型的推理配置。
    >
    > 有关设置这些变量的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>准备部署

在部署之前，必须确定将模型作为 Web 服务运行所需的内容。 以下列表描述了部署所需的基本项：

* 一个入口脚本  。 此脚本接受请求，使用模型为请求评分并返回结果。

    > [!IMPORTANT]
    > 入口脚本特定于你的模型；它必须能够识别传入请求数据的格式、模型所需数据的格式以及返回给客户端的数据的格式。
    >
    > 如果请求数据的格式对模型不可用，则该脚本可以将其转换为可接受的格式。 在将响应返回给客户端之前，它还可以对响应进行转换。
    >
    > 默认情况下，在为函数打包时，输入被视为文本。 如果想使用输入的原始字节（例如用于 Blob 触发器），则应使用 [AMLRequest 接受原始数据](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)。


* 依赖项，如运行入口脚本或模型所需的帮助程序脚本或 Python/Conda 包 

这些实体被封装到推理配置中  。 推理配置引用入口脚本和其他依赖项。

> [!IMPORTANT]
> 创建用于 Azure Functions 的推理配置时，需要使用 [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 对象。 请注意，如果要定义自定义环境，需要将版本不低于 1.0.45 的 azureml-defaults 添加为 Pip 依赖项。 此包包含将模型托管为 Web 服务所需的功能。 下面的示例演示如何创建环境对象并将其用于推理配置：
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到 Azure Functions 时，无需创建部署配置  。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安装 SDK 预览版包以获取函数支持

若要为 Azure Functions 生成包，需要安装 SDK 预览版包。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>创建映像

若想创建要部署到 Azure Functions 的 Docker 映像，请为想应用的触发器使用 [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) 或特定包函数。 下面的代码段演示如何通过模型和推理配置生成带有 blob 触发器的新包：

> [!NOTE]
> 代码段假定 `model` 包含已注册的模型，`inference_config` 包含推理环境的配置。 有关详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

当 `show_output=True` 时，将显示 Docker 生成过程的输出。 此过程完成后，即在 Azure 容器注册表中为工作区创建了映像。 映像生成后，会显示其在 Azure 容器注册表中的位置。 返回的位置采用 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 格式。

> [!NOTE]
> 函数打包当前支持 HTTP 触发器、Blob 触发器和服务总线触发器。 有关触发器的详细信息，请参阅 [Azure Functions 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)。

> [!IMPORTANT]
> 保存位置信息，因为会在部署映像时使用。

## <a name="deploy-image-as-a-web-app"></a>将映像部署为 Web 应用

1. 使用以下命令获取包含映像的 Azure 容器注册表的登录凭据。 将 `<myacr>` 替换为之前从 `package.location` 返回的值： 

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

    保存“用户名”和某个“密码”的值   。

1. 如果还没有用于部署服务的资源组或应用服务计划，可使用以下命令来创建这两项：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    此示例使用了“Linux 基本”  定价层级 (`--sku B1`)。

    > [!IMPORTANT]
    > Azure 机器学习创建的映像使用 Linux，因此需要使用 `--is-linux` 参数。

1. 创建要用于 Web 作业存储的存储帐户并获取其连接字符串。 将 `<webjobStorage>` 替换为要使用的名称。

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 使用以下命令来创建函数应用。 将 `<app-name>` 替换为要使用的名称。 将 `<acrinstance>` 和 `<imagename>` 替换为之前返回的 `package.location` 的值。 将 `<webjobStorage>` 替换为上一步中存储帐户的名称：

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 此时，函数应用已创建。 但是，由于你尚未向包含映像的 Azure 容器注册表提供 blob 触发器的连接字符串或凭据，因此函数应用未处于活动状态。 在接下来的步骤中，为容器注册表提供连接字符串和身份验证信息。 

1. 创建要用于 blob 触发器存储的存储帐户并获取其连接字符串。 将 `<triggerStorage>` 替换为要使用的名称。

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    记录要提供给函数应用的此连接字符串。 稍后在要求提供 `<triggerConnectionString>` 时会用到该字符串

1. 创建用于存储帐户中输入和输出信息的容器。 将 `<triggerConnectionString>` 替换为先前返回的连接字符串：

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 若要将触发器连接字符串与函数应用关联，请使用以下命令。 将 `<app-name>` 替换为函数应用的名称。 将 `<triggerConnectionString>` 替换为先前返回的连接字符串：

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 需要使用以下命令检索与创建的容器关联的标记。 将 `<username>` 替换为先前从容器注册表返回的用户名：

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    保存返回的值，它将在下一步中用作 `imagetag`。

1. 若要为函数应用提供访问容器注册表所需的凭据，请使用以下命令。 将 `<app-name>` 替换为函数应用的名称。 将 `<acrinstance>` 和 `<imagetag>` 替换为上一步中 AZ CLI 调用中的值。 将 `<username>` 和 `<password>` 替换为之前检索到的 ACR 登录信息：

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    此命令将返回类似以下 JSON 文档的信息：

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

此时，函数应用开始加载映像。

> [!IMPORTANT]
> 加载映像可能需要几分钟时间。 可以使用 Azure 门户监视进度。

## <a name="test-the-deployment"></a>测试部署

加载映像并且应用可用后，使用以下步骤来触发应用：

1. 创建一个文本文件，使其包含 score.py 文件所需的数据。 以下示例中的 score.py 需要其中包含 10 个数字的数组：

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > 数据的格式取决于你的 score.py 和模型所需的内容。

2. 使用以下命令将此文件上传到之前创建的触发器存储 blob 中的输入容器。 将 `<file>` 替换为包含数据的文件的名称。 将 `<triggerConnectionString>` 替换为先前返回的连接字符串。 在此示例中，`input` 是前面创建的输入容器的名称。 如果你使用了其他名称，请替换此值：

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    此命令的输出类似于以下 JSON：

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. 若要查看函数生成的输出，请使用以下命令列出生成的输出文件。 将 `<triggerConnectionString>` 替换为先前返回的连接字符串。 在此示例中，`output` 是前面创建的输出容器的名称。 如果你使用了其他名称，请替换此值：

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    此命令的输出类似于 `sample_input_out.json`。

4. 若要下载文件并检查内容，请使用以下命令。 将 `<file>` 替换为上一命令返回的文件名。 将 `<triggerConnectionString>` 替换为先前返回的连接字符串： 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    在该命令完成后，打开文件。 它包含模型返回的数据。

有关使用 blob 触发器的详细信息，请参阅[创建 Azure Blob 存储触发的函数](/azure/azure-functions/functions-create-storage-blob-triggered-function)一文。

## <a name="next-steps"></a>后续步骤

* 通过 [Functions](/azure/azure-functions/functions-create-function-linux-custom-image) 文档，了解如何配置 Functions 应用。
* 请参阅 [Azure Blob 存储绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)，详细了解 Blob 存储触发器。
* [将模型部署到 Azure 应用服务](how-to-deploy-app-service.md)。
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [API 参考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
