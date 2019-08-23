---
title: 将 ml 模型部署到 Azure App Service (预览版)
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务将模型部署到 Azure App Service 中的 Web 应用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897406"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>将机器学习模型部署到 Azure App Service (预览版)

了解如何在 Azure App Service 中以 web 应用的形式在 Azure 机器学习服务中部署模型。

> [!IMPORTANT]
> 尽管 Azure 机器学习服务和 Azure App Service 都已正式发布, 但将模型从机器学习服务部署到应用服务的功能处于预览阶段。

利用 Azure 机器学习 service, 你可以从经过训练的机器学习模型中创建 Docker 映像。 此映像包含一个 web 服务, 该服务接收数据、将数据提交到模型, 然后返回响应。 Azure App Service 可用于部署映像, 并提供以下功能:

* 增强安全性的高级[身份验证](/azure/app-service/configure-authentication-provider-aad)。 身份验证方法包括 Azure Active Directory 和多重身份验证。
* [自动缩放](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json), 无需重新部署。
* [SSL](/azure/app-service/app-service-web-ssl-cert-load)对客户端和服务之间的安全通信的支持。

有关 Azure App Service 提供的功能的详细信息, 请参阅[应用服务概述](/azure/app-service/overview)。

> [!IMPORTANT]
> 如果需要能够记录已部署的模型所使用的计分数据或评分结果, 应改为部署到 Azure Kubernetes 服务。 有关详细信息, 请参阅[收集生产模型上的数据](how-to-enable-data-collection.md)。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习服务工作区。 有关详细信息, 请参阅[创建工作区一](how-to-manage-workspace.md)文。
* 已在工作区中注册的经过训练的机器学习模型。 如果没有模型, 请使用[图像分类教程: 训练模型](tutorial-train-models-with-aml.md)训练和注册。

    > [!IMPORTANT]
    > 本文中的代码片段假设您已设置了以下变量:
    >
    > * `ws`-Azure 机器学习工作区。
    > * `model`-将要部署的注册模型。
    > * `inference_config`-模型的推理配置。
    >
    > 有关设置这些变量的详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>准备部署

在部署之前, 必须定义将模型作为 web 服务运行所需的内容。 以下列表描述了部署所需的基本项:

* 一个__项脚本__。 此脚本接受请求, 使用模型为请求评分, 并返回结果。

    > [!IMPORTANT]
    > 条目脚本特定于模型;它必须了解传入请求数据的格式、模型所需的数据格式, 以及返回给客户端的数据的格式。
    >
    > 如果请求数据的格式不能由您的模型使用, 则该脚本可以将其转换为可接受的格式。 它还可能在将响应返回给客户端之前对其进行转换。

    > [!IMPORTANT]
    > Azure 机器学习 SDK 不为 web 服务提供访问数据存储或数据集的方法。 如果需要部署的模型来访问在部署外部存储的数据, 例如在 Azure 存储帐户中, 则必须使用相关的 SDK 开发自定义代码解决方案。 例如,[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。
    >
    > 可能适用于你的方案的另一种方法是[批处理预测](how-to-run-batch-predictions.md), 这在评分时提供对数据存储的访问。

    有关输入脚本的详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

* 运行条目脚本或模型所需的**依赖项**(如帮助程序脚本或 Python/Conda 包)

这些实体封装为__推理配置__。 推理配置引用入口脚本和其他依赖项。

> [!IMPORTANT]
> 创建用于 Azure App Service 的推理配置时, 必须使用[环境](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)对象。 下面的示例演示如何创建环境对象并将其用于推理配置:
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

有关环境的详细信息, 请参阅[创建和管理用于定型和部署的环境](how-to-use-environments.md)。

有关推理配置的详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到 Azure App Service 时, 无需创建__部署配置__。

## <a name="create-the-image"></a>创建映像

若要创建部署到 Azure App Service 的 Docker 映像, 请使用[模型类](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-)。 下面的代码段演示如何从模型和推理配置生成新的图像:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

如果`show_output=True`为, 则显示 Docker 生成过程的输出。 完成此过程后, 将在工作区的 Azure 容器注册表中创建映像。

## <a name="deploy-image-as-a-web-app"></a>将映像部署为 web 应用

1. 在[Azure 门户](https://portal.azure.com)中, 选择 Azure 机器学习工作区。 从 "__概述__" 部分中, 使用__注册表__链接访问工作区的 Azure 容器注册表。

    [![工作区概述的屏幕截图](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. 从 Azure 容器注册表中, 选择 "__存储库__", 然后选择要部署的__映像名称__。 对于要部署的版本, 请选择 " __...__ " 条目, 然后将__其部署到 web 应用__。

    [![从 ACR 部署到 web 应用的屏幕截图](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. 若要创建 Web 应用, 请提供站点名称、订阅、资源组, 并选择 "应用服务计划/位置"。 最后，选择“创建”。

    !["新建 web 应用" 对话框的屏幕截图](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>使用 Web 应用

在[Azure 门户](https://portal.azure.com)中, 选择上一步中创建的 Web 应用。 从 "__概述__" 部分中, 复制__URL__。 此值为服务的__基 URL__ 。

[![Web 应用概述的屏幕截图](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

向模型传递请求的 web 服务位于`{baseurl}/score`。 例如， `https://mywebapp.azurewebsites.net/score` 。 以下 Python 代码演示了如何将数据提交到 URL 并显示响应:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* 有关配置 Web 应用的详细信息, 请参阅[Linux 上的应用服务](/azure/app-service/containers/)文档。
* 有关缩放的详细信息, 请参阅[Azure 中的自动缩放入门](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)。
* 有关 SSL 支持的详细信息, 请参阅[在 Azure App Service 中使用 SSL 证书](/azure/app-service/app-service-web-ssl-cert-load)。
* 有关身份验证的详细信息, 请参阅[将应用服务应用配置为使用 Azure Active Directory 登录](/azure/app-service/configure-authentication-provider-aad)。
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)