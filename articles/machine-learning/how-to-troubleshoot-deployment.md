---
title: Docker 部署故障排除
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习使用 Azure Kubernetes 服务和 Azure 容器实例解决、解决常见的 Docker 部署错误，并对其进行故障排除。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 82b9db2f3575e50367ed154246f9fb69b74c60cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333766"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>使用 Azure Kubernetes 服务和 Azure 容器实例对模型的 Docker 部署进行故障排除 

了解如何使用 Azure 机器学习排查、解决或规避 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 的常见 Docker 部署错误。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

    若要验证 Docker 安装，请使用终端或命令提示符中的命令 `docker run hello-world`。 有关安装 Docker 或排除 Dcoker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>机器学习模型的 Docker 部署步骤

在 Azure 机器学习中部署模型时，将使用 [模型。部署 ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 和 [环境](how-to-use-environments.md) 对象。 服务在部署阶段创建基本的 docker 映像，并在一次调用中装载所需的模型。 基本部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 定义推理配置：
    1. 创建 [环境](how-to-use-environments.md) 对象。 此对象可以使用环境 yaml 文件中的依赖项，特选环境之一。
    2. 基于环境和评分脚本创建推理配置（InferenceConfig 对象）。

3. 将模型部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="before-you-begin"></a>准备阶段

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

使用 Model 时，请使用[环境](how-to-use-environments.md)对象作为输入参数[部署 ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) ，你的代码可以分为三个主要步骤：

1. 注册模型。 下面是一些示例代码：

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 为部署定义推理配置：

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 使用在前面步骤中创建的推理配置来部署模型：

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

将三个部署过程分解成各个任务，可以更轻松地标识一些更常见的错误。

## <a name="debug-locally"></a>本地调试

如果在将模型部署到 ACI 或 AKS 时遇到问题，请将其部署为本地 web 服务。 使用本地 Web 服务可简化解决问题的过程。

可以在 [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 存储库中找到示例[本地部署笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb)，以探索可运行的示例。

> [!WARNING]
> 生成方案不支持本地 Web 服务部署。

若要进行本地部署，请修改代码以使用 `LocalWebservice.deploy_configuration()` 创建部署配置。 然后使用 `Model.deploy()` 部署该服务。 以下示例将模型（包含在 model 变量中）部署为本地 Web 服务：

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

如果要定义自己的 conda 规范 YAML，请列出 azureml-默认版本 >= 1.0.45 作为 pip 依赖项。 需要此包以 web 服务的形式承载模型。

此时，你可以正常使用该服务。 下面的代码演示如何将数据发送到服务：

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服务

在本地测试中，可能需要更新 `score.py` 文件以添加记录或尝试解决发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，以下代码重新加载服务的脚本，然后向其发送数据。 使用 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关将部署更新到其他计算目标的信息，请参阅[如何更新 webservice](how-to-deploy-update-web-service.md)。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 可从服务所使用的 `InferenceConfig` 对象指定的位置重新加载该脚本。

若要更改模型、Conda 依赖项或部署配置，请使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-)。 以下示例更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

要删除服务，请使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> 检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 以下示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
如果在日志中看到行 `Booting worker with pid: <pid>` 多次出现，则意味着没有足够的内存来启动辅助角色。
可以通过增加 `deployment_config` 中 `memory_gb` 的值来处理错误
 
## <a name="container-cannot-be-scheduled"></a>无法计划容器

将服务部署到 Azure Kubernetes Service 计算目标时，Azure 机器学习将尝试使用请求的资源量来计划服务。 如果在5分钟后群集中没有可用的合适资源量的节点，则部署将失败。 失败消息是 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 。 可以通过添加更多节点、更改节点的 SKU 或更改服务的资源要求来解决此错误。 

该错误消息通常会指示你更需要哪一种资源 - 例如，如果看到一条指示“`0/3 nodes are available: 3 Insufficient nvidia.com/gpu`”的错误消息，则意味着该服务需要 GPU，且群集中有三个节点没有可用的 GPU。 如果使用的是 GPU SKU，则可以通过添加更多节点来解决此问题；如果使用的不是 GPU SKU，则可以通过切换到启用 GPU 的 SKU，或将环境更改为不需要 GPU 来解决此问题。  

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

使用[检查 Docker 日志](#dockerlog)部分中的信息检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常情况下，在评分脚本的 `init()` 函数中，会调用 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 来查找容器中的模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹，函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

该示例将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为 DEBUG 可能会导致记录其他信息，这可能有助于识别故障。

## <a name="function-fails-runinput_data"></a>函数故障：run(input_data)

如果服务部署成功，但在向评分终结点发布数据时崩溃，可在 `run(input_data)` 函数中添加错误捕获语句，以便转而返回详细的错误消息。 例如：

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，不应在生产环境中按此方法返回错误消息。

## <a name="http-status-code-502"></a>HTTP 状态代码 502

502 状态代码指示服务在 score.py 文件的 `run()` 方法中引发了异常或已崩溃。 使用本文中的信息调试该文件。

## <a name="http-status-code-503"></a>HTTP 状态代码 503

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 自动缩放程序旨在处理负载中的 **渐变** 更改。 如果每秒收到大量请求，客户端可能会收到 HTTP 状态代码 503。 即使自动缩放程序快速响应，也需要 AKS 大量时间来创建其他容器。

要增加/减少的决策取决于当前容器副本的利用率。 处理请求)  (处理请求的副本数除以当前副本的总数是当前使用率。 如果此数目超过 `autoscale_target_utilization` ，则创建更多副本。 如果它较低，则减少副本。 用于添加副本的决策是预先的，并 (大约1秒钟) 。 若要删除副本，请 (大约1分钟) 。 默认情况下，自动缩放目标利用率设置为 **70%**，这意味着，服务可以处理每秒请求高峰 (RPS) **多达 30%**。

有两种方法可以帮助防止 503 状态代码：

> [!TIP]
> 这两种方法可以单独使用，也可以组合使用。

* 更改自动缩放创建新副本的利用率。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 该更改不会导致更快创建副本。 而会以较低的利用率阈值创建副本。 可以在利用率达到 30% 时，通过将值改为 30% 来创建副本，而不是等待该服务的利用率达到 70% 时再创建。
    
    如果 Web 服务已在使用当前最大副本，然后你仍能看见 503 状态代码，则请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本最小数量。 增加最小副本可提供一个更大的池来处理传入峰值。

    若要增加副本的最小数量，请将 `autoscale_min_replicas` 设置为更大的值。 可以使用以下代码计算所需的副本，将值替换为项目指定的值：

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 如果收到请求高峰大于新的最小副本可以处理的数量，则可能会再次收到 503 代码。 例如，服务流量增加时，可能需要增加最小副本数据。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) 模块参考。

## <a name="http-status-code-504"></a>HTTP 状态代码 504

504 状态代码指示请求已超时。默认超时值为 1 分钟。

可以通过修改 score.py 删除不必要的调用来增加超时值或尝试加快服务速度。 如果这些操作不能解决问题，请使用本文中的信息调试 score.py 文件。 代码可能处于无响应状态或无限循环。

## <a name="advanced-debugging"></a>高级调试

可能需要以交互方式调试模型部署中包含的 Python 代码。 例如，如果输入脚本失败，并且无法通过其他记录确定原因。 通过使用 Visual Studio Code 和 debugpy，可以附加到在 Docker 容器中运行的代码。

有关详细信息，请访问[在 VS Code 指南中进行交互式调试](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)。

## <a name="model-deployment-user-forum"></a>[模型部署用户论坛](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
