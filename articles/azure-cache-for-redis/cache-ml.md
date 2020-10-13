---
title: 将机器学习模型部署到使用 Redis 的 Azure Cache Azure Functions
description: 在本文中，你将使用 Redis 实例的 Azure 缓存，将模型从 Azure 机器学习作为函数应用部署 Azure Functions。 适用于 Redis 的 Azure 缓存具有极高的性能和可缩放性–当与 Azure 机器学习模型配对时，你的应用程序中将获得低延迟和高吞吐量。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 54109d5889ae2c08f444a3a089386d413bf4262b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650181"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>将机器学习模型部署到使用 Redis 的 Azure Cache Azure Functions 

在本文中，你将使用 Redis 实例的 Azure 缓存，将模型从 Azure 机器学习作为函数应用部署 Azure Functions。  

适用于 Redis 的 Azure 缓存具有极高的性能和可缩放性–当与 Azure 机器学习模型配对时，你的应用程序中将获得低延迟和高吞吐量。 缓存特别有用的几个方案是在推断数据和实际模型推理结果时。 在任一情况下，元数据或结果都存储在内存中，从而提高了性能。 

> [!NOTE]
> 虽然 Azure 机器学习和 Azure Functions 都已正式发布，但将模型从机器学习服务部署到 Functions 的功能目前处于预览阶段。  
>

## <a name="prerequisites"></a>必备条件
* Azure 订阅- [免费创建一个](https://azure.microsoft.com/free/)。
* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)一文。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* 工作区中注册的已训练的机器学习模型。 如果没有模型，请使用[图像分类教程：训练模型](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)来训练和注册模型。

> [!IMPORTANT]
> 本文中的代码片段假设你已设置以下变量：
>
> * `ws` - Azure 机器学习工作区。
> * `model` - 将要部署的注册模型。
> * `inference_config` - 用于模型的推理配置。
>
> 有关设置这些变量的详细信息，请参阅[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

## <a name="create-an-azure-cache-for-redis-instance"></a>创建用于 Redis 的 Azure 缓存实例 
你将能够部署机器学习模型，以便与任何基本、标准或高级缓存实例 Azure Functions。 若要创建缓存实例，请按照以下步骤操作。  

1. 中转到 Azure 门户主页或打开 "侧栏" 菜单，然后选择 " **创建资源**"。 
   
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **定价层** | 单击下拉箭头并选择一个[定价层](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

1. 选择“网络”选项卡，或单击页面底部的“网络”按钮 。

1. 在“网络”选项卡中，选择你的连接方法。

1. 选择页面底部的“下一步:高级”选项卡，或者单击页面底部的“下一步:高级”按钮。

1. 在基本或标准缓存实例的“高级”选项卡中，如果想要启用非 TLS 端口，请选择启用开关。

1. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据持久性的设置。

1. 选择页面底部的“下一步:标记”选项卡，或者单击“下一步:标记”按钮。

1. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

1. 选择“查看 + 创建”。 **** 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

1. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可在 Azure Cache for Redis 的“概述”页面上监视进度 ****  。 如果“状态”显示为“正在运行”，则表示该缓存可供使用。 ****   **** 

## <a name="prepare-for-deployment"></a>准备部署

在部署之前，必须确定将模型作为 Web 服务运行所需的内容。 以下列表描述了部署所需的核心项：

* 一个入口脚本  。 此脚本接受请求，使用模型为请求评分并返回结果。

    > [!IMPORTANT]
    > 入口脚本特定于你的模型；它必须能够识别传入请求数据的格式、模型所需数据的格式以及返回给客户端的数据的格式。
    >
    > 如果请求数据的格式对模型不可用，则该脚本可以将其转换为可接受的格式。 它还可能在将响应返回给客户端之前对其进行转换。
    >
    > 默认情况下，在为函数打包时，输入被视为文本。 如果想使用输入的原始字节（例如用于 Blob 触发器），则应使用 [AMLRequest 接受原始数据](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-advanced-entry-script#binary-data)。

对于 run 函数，请确保它连接到 Redis 终结点。

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

有关输入脚本的详细信息，请参阅 [定义计分代码。](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where?tabs=python#define-an-entry-script)

* 依赖项，如运行入口脚本或模型所需的帮助程序脚本或 Python/Conda 包****

这些实体被封装到推理配置中____。 推理配置引用入口脚本和其他依赖项。

> [!IMPORTANT]
> 创建用于 Azure Functions 的推理配置时，需要使用 [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) 对象。 请注意，如果要定义自定义环境，必须将版本不低于 1.0.45 的 azureml-defaults 添加为 pip 依赖项。 此包包含将模型作为 Web 服务托管时所需的功能。 下面的示例演示如何创建环境对象并将其用于推理配置：
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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)。

有关推理配置的详细信息，请参阅[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where?tabs=python#define-an-inference-configuration)。

> [!IMPORTANT]
> 部署到 Azure Functions 时，无需创建部署配置____。

## <a name="install-the-sdk-preview-package-for-functions-support"></a>安装 SDK 预览版包以获取函数支持

若要为 Azure Functions 生成包，需要安装 SDK 预览版包。

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>创建映像

若想创建要部署到 Azure Functions 的 Docker 映像，请为想应用的触发器使用 [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true) 或特定包函数。 下面的代码段演示如何使用来自模型和推理配置的 HTTP 触发器创建新包：

> [!NOTE]
> 该代码片段假定 `model` 包含已注册的模型，并且 `inference_config` 包含推理环境的配置。 有关详细信息，请参阅[使用 Azure 机器学习部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)。

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
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

    保存“用户名”和某个“密码”的值____ ____。

1. 如果你还没有资源组或应用服务计划来部署服务，以下命令将演示如何创建这两项：

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    此示例使用了“Linux 基本”定价层级 (`--sku B1`)。

    > [!IMPORTANT]
    > Azure 机器学习创建的映像使用 Linux，因此必须使用 `--is-linux` 参数。

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
    > 此时，函数应用已创建。 但是，由于尚未向包含映像的 Azure 容器注册表提供 HTTP 触发器的连接字符串或凭据，因此函数应用未处于活动状态。 在接下来的步骤中，为容器注册表提供连接字符串和身份验证信息。 

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
> 加载映像可能需要几分钟时间。 您可以使用 Azure 门户监视进度。

## <a name="test-azure-function-http-trigger"></a>测试 Azure Function HTTP 触发器 

现在，我们将运行并测试 Azure Function HTTP 触发器。

1. 在 Azure 门户中，请参阅 Azure Function app。
1. 在 "开发人员" 下，选择 " **代码 + 测试**"。 
1. 在右侧，选择 " **输入** " 选项卡。 
1. 单击 " **运行** " 按钮，测试 AZURE Function HTTP 触发器。 

现已成功使用 Azure Cache for Redis 实例将模型从 Azure 机器学习部署为函数应用。 通过导航到以下部分中的链接，了解有关适用于 Redis 的 Azure 缓存的详细信息。

## <a name="clean-up-resources"></a>清理资源

如果想要继续学习下一篇教程，可以保留本快速入门中创建的资源，以便重复使用。

否则，如果你已完成快速入门，可以删除在本快速入门中创建的 Azure 资源，以避免产生费用。 

> [!IMPORTANT]
> 删除资源组的操作不可逆。 删除资源组时，包含在其中的所有资源会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有资源组（其中包含要保留的资源）中为托管此示例而创建了相关资源，可从各自的边栏选项卡逐个删除这些资源，而不要删除资源组。

### <a name="to-delete-a-resource-group"></a>删除资源组的步骤

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。

2. 在“按名称筛选...”框中键入资源组的名称。 在资源组的结果列表中选择“...”，然后选择“删除资源组” 。

系统会要求确认是否删除资源组。 键入资源组的名称进行确认，然后选择“删除”。

片刻之后，将会删除该资源组及其所有资源。

## <a name="next-steps"></a>后续步骤 

* 详细了解 [适用于 Redis 的 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview)
* 通过 [Functions](/azure/azure-functions/functions-create-function-linux-custom-image) 文档，了解如何配置 Functions 应用。
* [API 参考](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true) 
* 创建 [使用适用于 Redis 的 Azure 缓存的 Python 应用](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-python-get-started)

