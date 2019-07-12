---
title: 部署故障排除指南
titleSuffix: Azure Machine Learning service
description: 了解如何解决和解决，并对使用 Azure Kubernetes 服务和 Azure 容器实例使用 Azure 机器学习服务的常见 Docker 部署错误进行故障排除。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707038"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure 机器学习服务的 Azure Kubernetes 服务和 Azure 容器实例部署故障排除

了解如何解决或解决使用 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 使用 Azure 机器学习服务的常见 Docker 部署错误。

在 Azure 机器学习服务中部署模型时，系统将执行大量任务。 部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 生成 Docker 映像，包括：
    1. 从注册表中下载已注册的模型。 
    2. 使用基于在环境 yaml 文件中指定的依赖项的 Python 环境创建 dockerfile。
    3. 添加在 dockerfile 中提供的模型文件和评分脚本。
    4. 使用 dockerfile 生成新的 Docker 映像。
    5. 向与工作区关联的 Azure 容器注册表注册 Docker 映像。

    > [!IMPORTANT]
    > 具体取决于你的代码，创建映像会自动发生而无需你输入。

3. 将 Docker 映像部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

4. 在 ACI 或 AKS 中启动一个（或多个）新容器。 

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="before-you-begin"></a>开始之前

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

分解为任务的部署是如果您使用的，很有帮助[Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API，或[Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API，为这两个函数执行与前面提到的步骤单个操作。 通常这些 Api 非常方便，但它可帮助分解步骤，通过将它们替换为进行故障排除时以下 API 调用。

1. 注册模型。 下面是一些示例代码：

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 生成映像。 下面是一些示例代码：

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 将映像部署为服务。 下面是一些示例代码：

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

将部署过程分解为单独任务后，可以查看部分最常见的错误。

## <a name="image-building-fails"></a>映像生成失败

如果无法生成 Docker 映像， [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)或[service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-)调用失败，并可提供一些线索某些错误消息。 还可以从图像生成日志中找到错误的更多详细信息。 下面是一些显示如何发现映像生成日志 URI 的代码示例。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

此映像日志 URI 是指向 Azure blob 存储中存储的日志文件的 SAS URL. 只需复制 URI 并将其粘贴到浏览器窗口，即可下载和查看日志文件。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure 密钥保管库访问策略和 Azure 资源管理器模板

图像生成也可能因 Azure 密钥保管库上的访问策略的问题。 使用 Azure 资源管理器模板创建工作区和关联的资源 （包括 Azure 密钥保管库），多个时间时，可能出现这种情况。 例如，使用模板多次使用相同的参数作为持续集成和部署管道的一部分。

通过模板的大多数资源创建操作是幂等的但密钥保管库中清除的访问策略每次使用模板。 清除策略中断到密钥保管库的访问任何现有的工作区与正在使用它。 当你尝试创建新的映像时，这种情况将导致错误。 可以收到的错误的示例如下：

__门户__：
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__：
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

若要避免此问题，我们建议以下方法之一：

* 不会部署该模板不止一次为相同的参数。 或使用模板来重新创建它们之前删除现有的资源。
* 检查密钥保管库访问策略，然后使用这些策略设置`accessPolicies`模板的属性。
* 检查是否已存在的密钥保管库资源。 如果是这样，不重新创建它通过模板。 例如，添加一个参数，可用于禁用创建密钥保管库资源，如果它已存在。

## <a name="debug-locally"></a>本地调试

如果遇到问题，将模型部署到 ACI 还是 AKS，请尝试将其部署为本地 web 服务。 使用本地 web 服务，可以更轻松地解决问题。 包含模型的 Docker 映像是下载并启动本地系统上。

> [!IMPORTANT]
> 本地 web 服务部署需要一个有效的本地系统上安装 Docker。 部署本地 web 服务之前，必须运行 docker。 有关安装和使用 Docker 的信息，请参阅[ https://www.docker.com/ ](https://www.docker.com/)。

> [!WARNING]
> 对于生产方案中不支持本地 web 服务部署。

若要将本地部署，修改代码以使用`LocalWebservice.deploy_configuration()`以创建部署配置。 然后，使用`Model.deploy()`部署服务。 下面的示例将模型部署 (包含在`model`变量) 作为本地 web 服务：

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

此时，您可以使用正常的服务。 例如，下面的代码演示了将数据发送到服务：

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>更新服务

在本地测试过程可能需要更新`score.py`文件以添加日志记录或尝试解决你发现的任何问题。 若要重新加载到的更改`score.py`文件，请使用`reload()`。 例如，下面的代码重新加载该服务，该脚本，然后将数据发送到它。 数据使用已更新评分`score.py`文件：

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 从指定的位置重新加载该脚本`InferenceConfig`服务使用的对象。

若要更改模型、 Conda 依赖项或部署配置，请使用[update （)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 以下示例将更新该服务使用的模型：

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

若要删除服务，请使用[delete （)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a id="dockerlog"></a> 检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 ACI、 AKS，和本地部署，可以查看日志。 下面的示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服务启动失败

已成功生成映像后，系统将尝试启动使用你的部署配置的容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误  。

使用中的信息[检查 Docker 日志](#dockerlog)部分，以检查日志。

## <a name="function-fails-getmodelpath"></a>函数故障：get_model_path()

通常情况下，在`init()`评分脚本中的函数[Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)函数调用以在容器中找到模型文件或包含模型文件的文件夹。 如果找不到模型文件或文件夹，该函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

此示例输出的本地路径 (相对于`/var/azureml-app`)，评分脚本需要找到模型文件或文件夹的容器中。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为调试可能会导致其他信息记入日志，这可能是标识故障很有用。

## <a name="function-fails-runinputdata"></a>函数故障：run(input_data)

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

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，您不应返回错误消息通过这种方式在生产环境中。

## <a name="http-status-code-503"></a>HTTP 状态代码 503

Azure Kubernetes 服务部署支持自动缩放，允许的副本，以添加以支持额外的负载。 但是，自动缩放程序设计为处理**逐步**负载中的更改。 如果每秒的请求中收到较大的峰值，客户端可能会收到 HTTP 状态代码 503。

有两件事情可以帮助防止 503 状态代码：

* 更改的自动缩放的利用率级别创建新的副本。
    
    默认情况下，设置自动缩放目标利用率为 70%，这意味着该服务可以处理剧增的每秒 (RPS) 最多 30%的请求数。 可以通过设置调整利用率目标`autoscale_target_utilization`到较低的值。

    > [!IMPORTANT]
    > 此更改不会导致要创建的副本*更快地*。 相反，它们是在较低的利用率阈值创建。 而不是等待，直到服务利用率的 70%时，将值更改为 30%会导致副本以 30%的利用率发生时创建。
    
    如果 web 服务已在使用当前的最大副本，并且你仍看到 503 状态代码，增加`autoscale_max_replicas`值以增加最大副本数。

* 更改副本的最小数目。 增加最小副本提供了更大的池来处理传入的负载高峰。

    若要增加的最小副本数，请设置`autoscale_min_replicas`到较高的值。 您可以通过使用下面的代码，计算所需的副本到你的项目将值替换特定的值：

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
    > 如果收到请求的峰值不是新的最小副本可以处理更大，可能会再次收到 503s年。 例如，与服务增加的流量，你可能需要增加最小副本。

有关详细信息设置`autoscale_target_utilization`， `autoscale_max_replicas`，并`autoscale_min_replicas`有关，请参阅[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模块引用。


## <a name="advanced-debugging"></a>高级调试

在某些情况下，您可能需要以交互方式调试您的模型部署中包含的 Python 代码。 例如，如果该条目脚本失败，并且不能通过附加的日志记录确定的原因。 通过使用 Visual Studio Code 和 Python 工具的 Visual Studio (PTVSD)，你可以附加到 Docker 容器内部运行的代码。

> [!IMPORTANT]
> 使用时的调试此方法不起作用`Model.deploy()`和`LocalWebservice.deploy_configuration`部署本地模型。 相反，必须创建映像使用[ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)类。 
>
> 本地 web 服务部署需要一个有效的本地系统上安装 Docker。 部署本地 web 服务之前，必须运行 docker。 有关安装和使用 Docker 的信息，请参阅[ https://www.docker.com/ ](https://www.docker.com/)。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地的 VS Code 开发环境中安装 Python 工具的 Visual Studio (PTVSD)，使用以下命令：

    ```
    python -m pip install --upgrade ptvsd
    ```

    在 VS Code 中使用 PTVSD 的详细信息，请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code 与 Docker 映像进行通信，请创建新的调试配置：

    1. 从 VS Code 中，选择__调试__菜单，然后选择__打开配置__。 名为的文件__launch.json__随即打开。

    1. 在中__launch.json__文件中，找到包含行`"configurations": [`，并在其后插入以下文本：

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
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
        > 如果已有的其他条目的配置部分中，您插入的代码后面添加逗号 （，）。

        本部分将附加到使用端口 5678 的 Docker 容器。

    1. 保存__launch.json__文件。

### <a name="create-an-image-that-includes-ptvsd"></a>创建包含 PTVSD 的映像

1. 修改你的部署的 conda 环境，使其包括 PTVSD。 下面的示例演示如何添加使用`pip_packages`参数：

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. 若要启动 PTVSD 并等待连接，在服务启动时，将以下代码添加到顶部你`score.py`文件：

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 在调试期间，可能想要在图像中的文件进行更改，而无需重新创建它。 若要安装 Docker 映像中的文本编辑器 (vim)，创建一个名为的新文本文件`Dockerfile.steps`并使用以下代码作为该文件的内容：

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    文本编辑器，可修改文件内容要测试更改，而无需创建新的映像的 docker 映像。

1. 若要创建使用的映像`Dockerfile.steps`文件，请使用`docker_file`参数创建的图像时。 下面的示例演示如何执行此操作：

    > [!NOTE]
    > 此示例假定`ws`指向在 Azure 机器学习工作区中，并且`model`是要部署的模型。 `myenv.yml`文件包含在步骤 1 中创建的 conda 依赖项。

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

一旦创建映像后，显示在注册表中的映像位置。 位置是类似于以下文本：

```text
myregistry.azurecr.io/myimage:1
```

在此文本的示例中，注册表名称是`myregistry`，并将映像命名为`myimage`。 映像版本是`1`。

### <a name="download-the-image"></a>下载映像

1. 打开命令提示符、 终端或其他命令行程序，并使用以下[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)命令向包含你的 Azure 机器学习工作区的 Azure 订阅进行身份验证：

    ```azurecli
    az login
    ```

1. 若要进行身份验证到 Azure 容器注册表 (ACR)，其中包含你的映像，使用以下命令。 替换为`myregistry`与其中一个时，返回已注册映像：

    ```azurecli
    az acr login --name myregistry
    ```

1. 若要下载到本地 Docker 映像，请使用以下命令。 替换为`myimagepath`时返回的位置中注册该映像：

    ```bash
    docker pull myimagepath
    ```

    映像路径应类似于`myregistry.azurecr.io/myimage:1`。 其中`myregistry`是你的注册表`myimage`是你的映像，和`1`是映像版本。

    > [!TIP]
    > 来自上一步的身份验证不会永远持续存在。 如果等待足够长的时间之间的身份验证命令和拉取命令，将收到身份验证失败。 如果发生这种情况，重新进行身份验证。

    完成下载所需的时间取决于你的 internet 连接速度。 下载状态显示在过程中。 下载完成后，可以使用`docker images`命令来验证是否已下载它。

1. 若要使其更轻松地使用该映像，使用以下命令以添加标记。 替换为`myimagepath`与步骤 2 中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    有关步骤的其余部分，可以访问本地映像作为`debug:1`而不是完整的映像路径值。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果设置中的 PTVSD 连接超时`score.py`文件，必须连接 VS Code 调试会话之前超时时间已到。 启动 VS Code 中，打开的本地副本`score.py`、 设置断点，并将其准备就绪之前使用本部分中的步骤。
>
> 调试并设置断点的详细信息，请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要开始使用的映像的 Docker 容器，请使用以下命令：

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要在容器内附加到 PTVSD 的 VS Code，打开 VS Code，并使用 F5 键或选择__调试__。 出现提示时，选择__Azure 机器学习服务：Docker 调试__配置。 此外可以从侧栏中，选择调试图标__Azure 机器学习服务：Docker 调试__中调试下拉列表菜单，然后使用绿色箭头，可将调试器附加的条目。

    ![调试图标、 开始调试按钮，并配置选择器](media/how-to-troubleshoot-deployment/start-debugging.png)

在此情况下，VS Code 连接到 PTVSD 在 Docker 容器，并在以前设置的断点处停止。 你现在可以逐句通过代码在其运行时，查看变量，等等。

使用 VS Code 调试 Python 的详细信息，请参阅[调试 Python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器文件

若要在图像中的文件进行更改，可以将附加到正在运行的容器，并执行 bash shell。 在这里，您可以使用 vim 编辑文件：

1. 若要连接到正在运行的容器，并启动容器中的 bash shell，请使用以下命令：

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要查找该服务使用的文件，请使用以下命令从容器中的 bash shell:

    ```bash
    cd /var/azureml-app
    ```

    在这里，可以使用 vim 编辑`score.py`文件。 使用 vim 的详细信息，请参阅[使用 Vim 编辑器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 通常情况下不保存到容器的更改。 若要保存的任何更改，使用以下命令，然后退出 shell 启动在前面步骤中 (也就是说，在另一个外壳程序中):

    ```bash
    docker commit debug debug:2
    ```

    此命令创建名为的新映像`debug:2`，其中包含你的编辑。

    > [!TIP]
    > 你将需要停止当前的容器，并开始使用新版本，才能使更改生效。

1. 请务必保留所做的更改到容器中的文件同步使用 VS 代码使用的本地文件。 否则，调试器体验将不会按预期工作。

### <a name="stop-the-container"></a>停止容器

若要停止容器，请使用以下命令：

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
