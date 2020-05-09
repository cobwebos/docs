---
title: 将 ML 模型部署到 Azure 应用服务（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习将模型部署到 Azure 应用服务中的 Web 应用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 646254238f83166c53fe94a1821c68ff4dac8f04
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651930"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>将机器学习模型部署到 Azure 应用服务（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何从 Azure 机器学习将模型部署为 Azure 应用服务中的 Web 应用。

> [!IMPORTANT]
> 尽管 Azure 机器学习和 Azure 应用服务都已正式发布，但将模型从机器学习服务部署到应用服务的功能处于预览阶段。

使用 Azure 机器学习，可通过经过训练的机器学习模型创建 Docker 映像。 此映像包含一个 Web 服务，它会接收数据，将数据提交到模型，然后返回响应。 Azure 应用服务可用于部署映像，并提供以下功能：

* 高级[身份验证](/azure/app-service/configure-authentication-provider-aad)，可增强安全性。 身份验证方法包括 Azure Active Directory 和多重身份验证。
* 无需重新部署即可[自动缩放](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* [TLS 支持](/azure/app-service/configure-ssl-certificate-in-code)客户端和服务之间的安全通信。

有关 Azure 应用服务提供的功能的详细信息，请参阅[应用服务概述](/azure/app-service/overview)。

> [!IMPORTANT]
> 如果需要记录已部署模型所使用的评分数据或评分结果的功能，应改为部署到 Azure Kubernetes 服务。 有关详细信息，请参阅[在生产模型上收集数据](how-to-enable-data-collection.md)。

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

    > [!IMPORTANT]
    > Azure 机器学习 SDK 不为 Web 服务提供访问数据存储或数据集的方法。 如果需要部署的模型访问存储在部署外的数据，例如 Azure 存储帐户中的数据，则必须使用相关的 SDK 开发自定义代码解决方案。 例如，[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。
    >
    > 可能适用于该方案的另一种方法是[批量预测](how-to-use-parallel-run-step.md)，它在评分时提供对数据存储的访问权限。

    有关入口脚本的详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

* 依赖项，如运行入口脚本或模型所需的帮助程序脚本或 Python/Conda 包 

这些实体被封装到推理配置中  。 推理配置引用入口脚本和其他依赖项。

> [!IMPORTANT]
> 创建用于 Azure 应用服务的推理配置时，必须使用 [Environment](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 对象。 请注意，如果要定义自定义环境，需要将版本不低于 1.0.45 的 azureml-defaults 添加为 Pip 依赖项。 此包包含将模型托管为 Web 服务所需的功能。 下面的示例演示如何创建环境对象并将其用于推理配置：
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> 部署到 Azure 应用服务时，无需创建部署配置  。

## <a name="create-the-image"></a>创建映像

若要创建部署到 Azure 应用服务的 Docker 映像，请使用 [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)。 下面的代码片段演示如何从模型和推理配置生成新的映像：

> [!NOTE]
> 代码段假定 `model` 包含已注册的模型，`inference_config` 包含推理环境的配置。 有关详细信息，请参阅[使用 Azure 机器学习部署模型](how-to-deploy-and-where.md)。

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

当 `show_output=True` 时，将显示 Docker 生成过程的输出。 此过程完成后，即在 Azure 容器注册表中为工作区创建了映像。 映像生成后，会显示其在 Azure 容器注册表中的位置。 返回的位置采用 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 格式。 例如，`myml08024f78fd10.azurecr.io/package@sha256:20190827151241` 。

> [!IMPORTANT]
> 保存位置信息，因为会在部署映像时使用。

## <a name="deploy-image-as-a-web-app"></a>将映像部署为 Web 应用

1. 使用以下命令获取包含映像的 Azure 容器注册表的登录凭据。 将 `<acrinstance>` 替换为之前从 `package.location` 返回的值：

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

    在此示例中，使用了基本定价层 ( __)__ `--sku B1`。

    > [!IMPORTANT]
    > Azure 机器学习创建的映像使用 Linux，因此需要使用 `--is-linux` 参数。

1. 若要创建 Web 应用，请使用以下命令。 将 `<app-name>` 替换为要使用的名称。 将 `<acrinstance>` 和 `<imagename>` 替换为之前返回的 `package.location` 的值：

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    此命令将返回类似以下 JSON 文档的信息：

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > 此时，已创建了 Web 应用。 但是，由于你尚未向包含映像的 Azure 容器注册表提供凭据，因此 Web 应用未处于活动状态。 在下一步中，为容器注册表提供身份验证信息。

1. 若要为 Web 应用提供访问容器注册表所需的凭据，请使用以下命令。 将 `<app-name>` 替换为要使用的名称。 将 `<acrinstance>` 和 `<imagename>` 替换为之前返回的 `package.location` 的值。 将 `<username>` 和 `<password>` 替换为之前检索到的 ACR 登录信息：

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

此时，Web 应用开始加载映像。

> [!IMPORTANT]
> 加载映像可能需要几分钟时间。 若要监视进度，请使用以下命令：
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 加载映像且站点处于活动状态后，该日志将显示一条消息，表明 `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`。

部署映像后，可以使用以下命令查找主机名：

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

此命令将返回类似以下主机名的信息 - `<app-name>.azurewebsites.net`。 将此值用作服务基 URL 的一部分  。

## <a name="use-the-web-app"></a>使用 Web 应用

向模型传递请求的 Web 服务位于 `{baseurl}/score`。 例如，`https://<app-name>.azurewebsites.net/score` 。 以下 Python 代码演示了如何将数据提交到 URL 并显示响应：

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>后续步骤

* 请参阅 [Linux 上的应用服务](/azure/app-service/containers/)文档，了解如何配置 Web 应用。
* 请参阅 [Azure 中的自动缩放入门](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)，详细了解缩放。
* [在 Azure App Service 中使用 TLS/SSL 证书](/azure/app-service/configure-ssl-certificate-in-code)。
* [将应用服务应用配置为使用 Azure Active Directory 登录](/azure/app-service/configure-authentication-provider-aad)。
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
