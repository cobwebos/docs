---
title: 部署故障排除指南
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习使用 Azure Kubernetes 服务和 Azure 容器实例解决、解决常见的 Docker 部署错误，并对其进行故障排除。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: bf86826d77c690b60c7b091d6250a85fffd21fc0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896338"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure 机器学习 Azure Kubernetes 服务和 Azure 容器实例部署的故障排除

了解如何使用 Azure 机器学习在 Azure 容器实例（ACI）和 Azure Kubernetes Service （AKS）上解决或解决常见的 Docker 部署错误。

在 Azure 机器学习中部署模型时，系统将执行多个任务。

对于模型部署，推荐的和最新的方法是通过使用[环境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)对象作为输入参数的[deploy （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API。 在这种情况下，我们的服务将在部署阶段为你创建基本的 docker 映像，并在一次调用中装载所需的模型。 基本部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 定义推理配置：
    1. 根据你在环境 yaml 文件中指定的依赖项创建[环境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)对象，或使用我们的一个采购环境。
    2. 基于环境和评分脚本创建推理配置（InferenceConfig 对象）。

3. 将模型部署到 Azure 容器实例（ACI）服务或 Azure Kubernetes 服务（AKS）。

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="prerequisites"></a>必备组件

* 一个 **Azure 订阅**。 如果没有，请尝试[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。
* [AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，您必须在您的本地系统上安装一个工作 Docker。

    若要验证 Docker 安装，请从终端或命令提示符使用命令 `docker run hello-world`。 有关安装 Docker 或排查 Docker 错误的信息，请参阅[Docker 文档](https://docs.docker.com/)。

## <a name="before-you-begin"></a>开始之前

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

假设你使用的是新的/推荐[的部署方法](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)，并使用[环境](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments)对象作为输入参数，则可以将你的代码划分为三个主要步骤：

1. 注册模型。 下面是一些示例代码：

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 定义部署的推理配置：

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 使用在上一步中创建的推理配置部署模型：

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

如果在将模型部署到 ACI 或 AKS 时遇到问题，请尝试将其部署为本地 web 服务。 使用本地 web 服务，可以更轻松地解决问题。 包含模型的 Docker 映像将下载并在本地系统上启动。

> [!WARNING]
> 生产方案不支持本地 web 服务部署。

若要在本地部署，请修改代码以使用 `LocalWebservice.deploy_configuration()` 创建部署配置。 然后使用 `Model.deploy()` 部署该服务。 下面的示例将模型（包含在模型变量中）部署为本地 web 服务：

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

请注意，如果定义自己的 conda 规范 YAML，则必须使用版本 > = 1.0.45 作为 pip 依赖项列出 azureml 默认值。 此包包含将模型托管为 web 服务所需的功能。

此时，您可以正常使用该服务。 例如，以下代码演示了如何将数据发送到服务：

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

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于定型和部署的环境](how-to-use-environments.md)。 

### <a name="update-the-service"></a>更新服务

在本地测试过程中，可能需要更新 `score.py` 文件，以添加日志记录或尝试解决已发现的任何问题。 若要重新加载对 `score.py` 文件的更改，请使用 `reload()`。 例如，下面的代码重新加载该服务的脚本，然后向其发送数据。 使用更新后的 `score.py` 文件对数据进行评分：

> [!IMPORTANT]
> `reload` 方法仅适用于本地部署。 有关将部署更新到另一个计算目标的信息，请参阅[部署模型](how-to-deploy-and-where.md#update)的 "更新" 部分。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 脚本将从服务所使用的 `InferenceConfig` 对象指定的位置重新加载。

若要更改模型、Conda 依赖项或部署配置，请使用[update （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 下面的示例将更新服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

若要删除服务，请使用[delete （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a id="dockerlog"></a>检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 下面的示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后，系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

使用[检查 Docker 日志](#dockerlog)部分中的信息来检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常，在评分脚本的 `init()` 函数中，调用了[get_model_path （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)函数，以在容器中查找模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹，该函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

此示例在你的计分脚本需要在其上查找模型文件或文件夹的容器中输出本地路径（相对于 `/var/azureml-app`）。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

如果将日志记录级别设置为 "调试"，则可能会导致记录额外的信息，这可能有助于识别故障。

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

注意：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，不应在生产环境中以这种方式返回错误消息。

## <a name="http-status-code-503"></a>HTTP 状态代码503

Azure Kubernetes 服务部署支持自动缩放，这允许添加副本以支持额外的负载。 但是，自动缩放程序设计用于处理负载中的**渐变**更改。 如果每秒收到的请求数过大，客户端可能会收到 HTTP 状态代码503。

可以通过两种方式来防止503状态代码：

* 更改自动缩放功能创建新副本的利用率级别。
    
    默认情况下，自动缩放目标利用率设置为70%，这意味着服务可处理多达30% 的每秒请求数（RPS）的高峰。 可以通过将 `autoscale_target_utilization` 设置为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 此更改不会导致*更快地*创建副本。 而是以较低的利用率阈值创建。 将值更改为30% 会导致在使用率达到30% 时创建副本，而不是等待服务达到70% 的利用率。
    
    如果 web 服务已在使用当前的最大副本，但仍看到503状态代码，请增加 `autoscale_max_replicas` 值以增加副本的最大数量。

* 更改副本的最小数目。 增加最小副本可提供更大的池来处理传入峰值。

    若要增加最小数量的副本，请将 `autoscale_min_replicas` 设置为较高的值。 可以使用以下代码来计算所需的副本，并将值替换为项目特定的值：

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
    > 如果收到的请求高峰大于新的最小副本可以处理的数量，则可能会再次收到503s。 例如，当服务的流量增加时，可能需要增加最小副本。

有关设置 `autoscale_target_utilization`、`autoscale_max_replicas`和 `autoscale_min_replicas` 的详细信息，请参阅[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模块参考。

## <a name="advanced-debugging"></a>高级调试

在某些情况下，可能需要以交互方式调试模型部署中包含的 Python 代码。 例如，如果输入脚本失败，原因无法由其他日志记录确定。 通过使用 Visual Studio Code 和针对 Visual Studio 的 Python 工具（PTVSD），你可以附加到在 Docker 容器中运行的代码。

> [!IMPORTANT]
> 当使用 `Model.deploy()` 和 `LocalWebservice.deploy_configuration` 在本地部署模型时，此调试方法不起作用。 相反，必须使用[Model （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)方法创建映像。

本地 web 服务部署需要在你的本地系统上进行工作的 Docker 安装。 有关使用 Docker 的详细信息，请参阅[Docker 文档](https://docs.docker.com/)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地 VS Code 开发环境中安装针对 Visual Studio 的 Python 工具（PTVSD），请使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关将 PTVSD 与 VS Code 一起使用的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code 以与 Docker 映像通信，请创建新的调试配置：

    1. 在 VS Code 中，选择 "__调试__" 菜单，然后选择 "__打开配置__"。 将打开一个名为 "__启动__" 的文件。

    1. 在 "__启动 json__文件" 中，找到包含 `"configurations": [`的行，并在其后面插入以下文本：

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
        > 如果 "配置" 部分中已有其他项，请在插入的代码后添加一个逗号（，）。

        本部分使用端口5678附加到 Docker 容器。

    1. 保存__启动__文件。

### <a name="create-an-image-that-includes-ptvsd"></a>创建包含 PTVSD 的映像

1. 修改部署的 conda 环境，使其包括 PTVSD。 下面的示例演示如何使用 `pip_packages` 参数添加它：

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

1. 基于环境定义创建一个映像，并将该映像提取到本地注册表。 在调试过程中，你可能需要对映像中的文件进行更改，而不必重新创建。 若要在 Docker 映像中安装文本编辑器（vim），请使用 `Environment.docker.base_image` 和 `Environment.docker.base_dockerfile` 属性：

    > [!NOTE]
    > 此示例假定 `ws` 指向 Azure 机器学习工作区，`model` 是要部署的模型。 `myenv.yml` 文件包含在步骤1中创建的 conda 依赖项。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = NONE
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    创建和下载映像后，图像路径（包括存储库、名称和标记，在此示例中也是摘要）会显示在类似于以下内容的消息中：

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 若要更轻松地处理图像，请使用以下命令添加标记。 将 `myimagepath` 替换为上一步中的 location 值。

    ```bash
    docker tag myimagepath debug:1
    ```

    对于其余步骤，你可以将本地映像作为 `debug:1` 而不是完整的图像路径值来引用。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果在 `score.py` 文件中设置 PTVSD 连接的超时值，则必须在超时过期之前将 VS Code 连接到调试会话。 开始 VS Code，打开 `score.py`的本地副本，设置一个断点，并在使用本节中的步骤之前准备就绪。
>
> 有关调试和设置断点的详细信息，请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像启动 Docker 容器，请使用以下命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要将 VS Code 附加到容器中的 PTVSD，请打开 VS Code 并使用 F5 键或选择 "__调试__"。 出现提示时，请选择 " __Azure 机器学习： Docker 调试__配置"。 还可以从侧栏中选择 "调试" 图标，从 "调试" 下拉菜单中选择 " __Azure 机器学习： Docker 调试__" 项，然后使用绿色箭头附加调试器。

    !["调试" 图标、"启动调试" 按钮和配置选择器](./media/how-to-troubleshoot-deployment/start-debugging.png)

此时，VS Code 连接到 Docker 容器内部的 PTVSD，并在之前设置的断点处停止。 现在可以单步执行代码，就像运行、查看变量等。

有关使用 VS Code 调试 Python 的详细信息，请参阅[调试 python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器文件

若要对图像中的文件进行更改，可以附加到正在运行的容器，然后执行 bash shell。 在这里，你可以使用 vim 编辑文件：

1. 若要连接到正在运行的容器并在容器中启动 bash shell，请使用以下命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要查找服务使用的文件，请在容器中使用 bash shell 中的以下命令（如果默认目录不同于 `/var/azureml-app`）：

    ```bash
    cd /var/azureml-app
    ```

    在这里，你可以使用 vim 来编辑 `score.py` 文件。 有关使用 vim 的详细信息，请参阅[使用 vim 编辑器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 通常不会保留对容器所做的更改。 若要保存所做的任何更改，请使用以下命令，然后在退出前面步骤中（即，另一个 shell 中）启动的 shell 之前：

    ```bash
    docker commit debug debug:2
    ```

    此命令将创建一个名为 `debug:2` 的新映像，其中包含你的编辑内容。

    > [!TIP]
    > 需要停止当前容器并开始使用新版本，然后更改才会生效。

1. 请确保将对容器中的文件所做的更改与 VS Code 使用的本地文件保持同步。 否则，调试器体验将不会按预期工作。

### <a name="stop-the-container"></a>停止容器

若要停止容器，请使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练 & 部署模型](tutorial-train-models-with-aml.md)
