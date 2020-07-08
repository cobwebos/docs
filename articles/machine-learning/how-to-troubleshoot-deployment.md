---
title: Docker 部署故障排除
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习使用 Azure Kubernetes 服务和 Azure 容器实例解决、解决常见的 Docker 部署错误，并对其进行故障排除。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 13ce9204ad09d2ecb4b149cf50696aa73d927314
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214360"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>排查通过 Azure Kubernetes 服务和 Azure 容器实例进行的模型 Docker 部署 

了解如何使用 Azure 机器学习对 Azure 容器实例（ACI）和 Azure Kubernetes 服务（AKS）进行常见的 Docker 部署错误的疑难解答和解决。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。
* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

    若要验证 Docker 安装，请从终端或命令提示符使用命令 `docker run hello-world`。 有关安装 Docker 或排除 Docker 错误的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>适用于 Docker 部署机器学习模型的步骤

在 Azure 机器学习中部署模型时，系统将执行大量任务。

建议模型部署的方法是通过使用[环境](how-to-use-environments.md)对象作为输入参数的[deploy （） API。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 在这种情况下，服务会在部署阶段创建基本的 docker 映像，并在一次调用中装载所需的模型。 基本部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 定义推理配置：
    1. 根据你在环境 yaml 文件中指定的依赖项创建一个 [Environment](how-to-use-environments.md) 对象，或者使用我们获得的环境之一。
    2. 基于环境和评分脚本创建推理配置（InferenceConfig 对象）。

3. 将模型部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="before-you-begin"></a>开始之前

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

假设你使用新的/推荐的部署方法，也就是使用 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API 并将 [Environment](how-to-use-environments.md) 对象作为输入参数，则你的代码可以分解为三个主要步骤：

1. 注册模型。 以下是一些示例代码：

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

3. 使用上一步中创建的推理配置来部署模型：

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

将部署过程分解为单独任务后，可以查看部分最常见的错误。

## <a name="debug-locally"></a>本地调试

如果将模型部署到 ACI 或 AKS 时遇到问题，请尝试将其部署为本地 Web 服务。 使用本地 Web 服务可以更容易地排除问题。 包含模型的 Docker 映像将下载到本地系统，并在本地系统上启动。

> [!WARNING]
> 生产方案不支持本地 Web 服务部署。

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

如果要定义自己的 conda 规范 YAML，必须使用版本 >= 1.0.45 作为 pip 依赖项列出 azureml 默认值。 此包包含将模型托管为 Web 服务所需的功能。

此时，你可以正常使用该服务。 例如，以下代码演示了将数据发送到该服务的过程：

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

在本地测试期间，可能需要更新 `score.py` 文件以添加记录或尝试解决发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，以下代码重新加载服务的脚本，然后向其发送数据。 使用更新后的 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关将部署更新到其他计算目标的信息，请参阅[部署模型](how-to-deploy-and-where.md#update)的“更新”部分。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 脚本从服务所使用的 `InferenceConfig` 对象指定的位置重新加载。

若要更改模型、Conda 依赖项或部署配置，请使用 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 以下示例更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

若要删除服务，请使用 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和本地部署的日志。 以下示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
## <a name="container-cannot-be-scheduled"></a>无法计划容器

将服务部署到 Azure Kubernetes Service 计算目标时，Azure 机器学习将尝试使用请求的资源量来计划服务。 如果在5分钟后，群集中没有可用的可用资源量的节点，则部署将失败，并显示消息 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 。 可通过添加更多节点、更改节点的 SKU 或更改服务的资源要求来解决此错误。 

例如，如果看到一条错误消息，指出该 `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 服务需要 gpu，并且群集中有三个节点没有可用 gpu，则该错误消息通常会指示需要更多的资源。 如果使用的是 GPU SKU，则可以通过添加更多节点来解决此问题；如果使用的不是 GPU SKU，则可以通过切换到启用 GPU 的 SKU，或将环境更改为不需要 GPU 来解决此问题。  

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

使用[检查 Docker 日志](#dockerlog)部分中的信息检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常情况下，在评分脚本的 `init()` 函数中，会调用 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 函数来查找容器中的模型文件或模型文件所在的文件夹。 如果找不到模型文件或文件夹，函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

此示例将打印容器中的本地路径（相对于 `/var/azureml-app`），评分脚本应可在此路径中找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

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

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 但是，自动缩放程序旨在处理负载中的逐步更改。 如果每秒收到大量请求，客户端可能会收到 HTTP 状态代码 503。

有两种方法可以帮助防止 503 状态代码：

* 更改自动缩放创建新副本的利用率级别。
    
    默认情况下，自动缩放目标利用率设置为 70%，这意味着服务可以处理高达 30% 的 峰值每秒请求数 (RPS)。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 这项更改不会导致更快创建副本。 反而会以较低的利用率阈值创建副本。 可以在利用率达到 30% 时，通过将值改为 30% 来创建副本，而不是等待该服务的利用率达到 70% 时再创建。
    
    如果 Web 服务已在使用当前最大副本数量，而你仍会看到 503 状态代码，请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本最小数量。 增加最小副本数量可以提供一个更大的池来处理传入峰值。

    若要增加副本的最小数量，请将 `autoscale_min_replicas` 设置为更大的值。 可以使用以下代码计算所需的副本数量，将值替换为特定于项目的值：

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
    > 如果收到的峰值请求数大于新的最小副本数可以处理的数量，则可能会再次收到 503 代码。 例如，服务流量增加时，可能需要增加最小副本数量。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas` 和 `autoscale_min_replicas` 的详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 模块参考。

## <a name="http-status-code-504"></a>HTTP 状态代码 504

504 状态代码指示请求已超时。默认超时值为 1 分钟。

可通过修改 score.py，删除不必要的调用来增加超时值或尝试加快服务速度。 如果这些操作不能解决问题，请使用本文中的信息调试 score.py 文件。 代码可能处于无响应状态或无限循环。

## <a name="advanced-debugging"></a>高级调试

某些情况下，可能需要以交互方式调试模型部署中包含的 Python 代码。 例如，输入脚本失败，并且无法通过其他记录确定原因的情况。 通过使用 Visual Studio Code 和针对 Visual Studio 的 Python 工具 (PTVSD)，可以附加到在 Docker 容器中运行的代码。

> [!IMPORTANT]
> 使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 在本地部署模型时，此调试方法不起作用。 相反，你必须使用 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) 方法创建一个映像。

若要在本地部署 Web 服务，需要在本地系统上安装能够正常工作的 Docker。 有关使用 Docker 的详细信息，请参阅 [Docker 文档](https://docs.docker.com/)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地 VS Code 部署环境中安装针对 Visual Studio 的 Python 工具 (PTVSD)，请使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关结合使用 PTVSD 和 VS Code 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code，使其与 Docker 映像进行通信，请创建新的调试配置：

    1. 在 VS Code 中，选择“调试”菜单，然后选择“打开配置” 。 将打开一个名为 launch.json 的文件。

    1. 在 launch.json 文件中，找到包含 `"configurations": [` 的行，然后在其后插入以下文本：

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 如果“配置”部分已存在其他条目，请在插入的代码后添加一个逗号 (,)。

        本部分使用端口 5678 附加到 Docker 容器。

    1. 保存 launch.json 文件。

### <a name="create-an-image-that-includes-ptvsd"></a>创建包含 PTVSD 的映像

1. 修改部署的 Conda 环境，使其包括 PTVSD。 以下示例演示使用 `pip_packages` 参数添加它的过程：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要在服务启动时启动 PTVSD 并等待连接，请将以下内容添加到 `score.py` 文件的顶部：

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 基于环境定义创建一个映像，并将该映像提取到本地注册表。 在调试过程中，你可能需要对映像中的文件进行更改，而不必重新创建文件。 若要在 Docker 映像中安装文本编辑器 (vim)，请使用 `Environment.docker.base_image` 和 `Environment.docker.base_dockerfile` 属性：

    > [!NOTE]
    > 此示例假定 `ws` 指向 Azure 机器学习工作区，且 `model` 是要部署的模型。 `myenv.yml` 文件包含步骤 1 中创建的 Conda 依赖项。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    创建并下载映像后，映像路径（包括存储库、名称和标记，在此示例中为摘要信息）会显示在类似于如下所示的消息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 如果希望简化映像的操作，请使用以下命令添加标记。 将 `myimagepath` 替换为上一步中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    对于其余步骤，可以将本地映像作为 `debug:1` 而不是完整的映像路径值来进行引用。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果在 `score.py` 文件中为 PTVSD 连接设置超时，则必须在超时到期之前将 VS Code 连接到调试会话。 启动 VS Code，打开 `score.py` 的本地副本，设置一个断点，使其准备就绪，然后再使用本部分中的步骤进行操作。
>
> 有关调试和设置断点的详细信息，请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像启动 Docker 容器，请使用以下命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要将 VS Code 附加到容器中的 PTVSD，请打开 VS Code 并按 F5 或选择“调试”。 出现提示时，请选择“Azure 机器学习：Docker 调试”配置。 还可以从侧栏中选择“调试”图标，即“Azure 机器学习：Docker 调试”项（位于“调试”下拉菜单中），然后使用绿色箭头附加调试程序。

    ![“调试”图标、“启动调试”按钮和配置选择器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此时，VS Code 会连接到 Docker 容器内的 PTVSD，并在之前设置的断点处停止。 现在，你可以在代码运行时逐句调试代码、进行查看变量等操作。

有关使用 VS Code 调试 Python 的详细信息，请参阅[调试 Python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器文件

若要更改映像中的文件，可以附加到正在运行的容器，并执行 bash shell。 在这里，你可以使用 vim 编辑文件：

1. 若要连接正在运行的容器，并在容器中启动 bash shell，请使用以下命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要查找服务使用的文件，请在容器中使用 bash shell 的以下命令（如果默认目录与 `/var/azureml-app` 不同）：

    ```bash
    cd /var/azureml-app
    ```

    在这里，你可以使用 vim 编辑 `score.py` 文件。 有关使用 vim 的详细信息，请参阅[使用 Vim 编辑器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 通常不会保留对容器所做的更改。 若要保存所做的任何更改，请在退出上一个步骤中（即另一个 shell 中）启动的 shell 之前，使用以下命令：

    ```bash
    docker commit debug debug:2
    ```

    此命令创建一个名为 `debug:2` 的新映像，其中包含你进行的编辑。

    > [!TIP]
    > 需要停止当前容器并开始使用新版本，更改才会生效。

1. 请确保将对容器中的文件所做的更改与 VS Code 使用的本地文件保持同步。 否则，调试程序体验将达不到预期效果。

### <a name="stop-the-container"></a>停止容器

若要停止容器，请使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
