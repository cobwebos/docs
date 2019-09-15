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
ms.date: 07/09/2019
ms.custom: seodec18
ms.openlocfilehash: 08b9434dbcca96ff57e2c8182693023a5eb2eea9
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997159"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure 机器学习 Azure Kubernetes 服务和 Azure 容器实例部署的故障排除

了解如何使用 Azure 机器学习在 Azure 容器实例（ACI）和 Azure Kubernetes Service （AKS）上解决或解决常见的 Docker 部署错误。

在 Azure 机器学习中部署模型时，系统将执行多个任务。 部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 生成 Docker 映像，包括：
    1. 从注册表中下载已注册的模型。 
    2. 使用基于在环境 yaml 文件中指定的依赖项的 Python 环境创建 dockerfile。
    3. 添加在 dockerfile 中提供的模型文件和评分脚本。
    4. 使用 dockerfile 生成新的 Docker 映像。
    5. 向与工作区关联的 Azure 容器注册表注册 Docker 映像。

    > [!IMPORTANT]
    > 根据你的代码, 将自动创建映像, 而不进行输入。

3. 将 Docker 映像部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

4. 在 ACI 或 AKS 中启动一个（或多个）新容器。 

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="before-you-begin"></a>开始之前

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。

如果你使用的是[webservice （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) api 或[deploy_from_model （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) api，则将部署分解成任务非常有用，因为这两个函数都作为单个操作执行上述步骤。 通常, 这些 Api 是非常方便的, 但通过将它们替换为以下 API 调用, 有助于在排除故障时分解这些步骤。

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

如果无法生成 Docker 映像， [wait_for_creation （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)或[wait_for_deployment （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-)调用将失败，并会出现一些可能提供某些线索的错误消息。 还可以从图像生成日志中找到错误的更多详细信息。 下面是一些显示如何发现映像生成日志 URI 的代码示例。

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

此映像日志 URI 是指向 Azure blob 存储中存储的日志文件的 SAS URL. 只需复制 URI 并将其粘贴到浏览器窗口，即可下载和查看日志文件。

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

由于 Azure Key Vault 上的访问策略有问题, 映像生成也可能会失败。 当你使用 Azure 资源管理器模板多次创建工作区和关联资源 (包括 Azure Key Vault) 时, 可能会出现这种情况。 例如, 将模板多次用于与持续集成和部署管道的一部分的相同参数。

大多数通过模板创建的资源操作都是幂等的, 但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会断开使用该访问策略的任何现有工作区对 Key Vault 的访问。 这种情况会导致在您尝试创建新映像时出现错误。 下面是可以接收的错误的示例:

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

若要避免此问题, 建议采用以下方法之一:

* 不要对相同的参数多次部署模板。 或删除现有资源, 然后使用模板重新创建它们。
* 检查 Key Vault 访问策略, 然后使用这些策略设置`accessPolicies`模板的属性。
* 检查 Key Vault 资源是否已存在。 如果是这样, 请不要通过模板重新创建它。 例如, 添加一个参数, 该参数允许你禁用 Key Vault 资源的创建 (如果已存在)。

## <a name="debug-locally"></a>本地调试

如果在将模型部署到 ACI 或 AKS 时遇到问题, 请尝试将其部署为本地 web 服务。 使用本地 web 服务, 可以更轻松地解决问题。 包含模型的 Docker 映像将下载并在本地系统上启动。

> [!IMPORTANT]
> 本地 web 服务部署需要在你的本地系统上进行工作的 Docker 安装。 在部署本地 web 服务之前, Docker 必须处于运行状态。 有关安装和使用 Docker 的信息, 请[https://www.docker.com/](https://www.docker.com/)参阅。

> [!WARNING]
> 生产方案不支持本地 web 服务部署。

若要在本地部署, 请修改代码`LocalWebservice.deploy_configuration()`以使用来创建部署配置。 然后, `Model.deploy()`使用部署该服务。 下面的示例将模型 (包含在`model`变量中) 部署为本地 web 服务:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

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

此时, 您可以正常使用该服务。 例如, 以下代码演示了如何将数据发送到服务:

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

### <a name="update-the-service"></a>更新服务

在本地测试过程中, 可能需要更新`score.py`文件, 以添加日志记录或尝试解决已发现的任何问题。 若要重新加载对`score.py`文件的更改`reload()`, 请使用。 例如, 下面的代码重新加载该服务的脚本, 然后向其发送数据。 使用更新`score.py`的文件对数据进行评分:

> [!IMPORTANT]
> `reload`方法仅适用于本地部署。 有关将部署更新到另一个计算目标的信息，请参阅[部署模型](how-to-deploy-and-where.md#update)的 "更新" 部分。

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 此脚本将从服务所使用的`InferenceConfig`对象所指定的位置重新加载。

若要更改模型、Conda 依赖项或部署配置, 请使用[update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)。 下面的示例将更新服务使用的模型:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>删除服务

若要删除服务, 请使用[delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)。

### <a id="dockerlog"></a>检查 Docker 日志

可以通过服务对象打印详细的 Docker 引擎日志消息。 可以查看 ACI、AKS 和 Local 部署的日志。 下面的示例演示如何打印日志。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>服务启动失败

成功生成映像后, 系统会尝试使用部署配置启动容器。 作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。

使用[检查 Docker 日志](#dockerlog)部分中的信息来检查日志。

## <a name="function-fails-get_model_path"></a>函数故障：get_model_path()

通常，在`init()`计分脚本的函数中，将调用[_model_path （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)函数，以在容器中查找模型文件或模型文件的文件夹。 如果找不到模型文件或文件夹, 该函数将失败。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

此示例在你的计分脚本需要在其`/var/azureml-app`上查找模型文件或文件夹的容器中输出本地路径 (相对于)。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

如果将日志记录级别设置为 "调试", 则可能会导致记录额外的信息, 这可能有助于识别故障。

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

**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因, 不应在生产环境中以这种方式返回错误消息。

## <a name="http-status-code-503"></a>HTTP 状态代码503

Azure Kubernetes 服务部署支持自动缩放, 这允许添加副本以支持额外的负载。 但是, 自动缩放程序设计用于处理负载中的**渐变**更改。 如果每秒收到的请求数过大, 客户端可能会收到 HTTP 状态代码503。

可以通过两种方式来防止503状态代码:

* 更改自动缩放功能创建新副本的利用率级别。
    
    默认情况下，自动缩放目标利用率设置为 70%，这意味着服务可处理多达 30% 的每秒请求数（RPS）的高峰。 可以通过将设置`autoscale_target_utilization`为较低的值来调整利用率目标。

    > [!IMPORTANT]
    > 此更改不会导致*更快地*创建副本。 而是以较低的利用率阈值创建。 将值更改为 30% 会导致在使用率达到 30% 时创建副本，而不是等待服务达到 70% 的利用率。
    
    如果 web 服务已在使用当前的最大副本, 但仍看到503状态代码, 请增加`autoscale_max_replicas`该值以增加副本的最大数量。

* 更改副本的最小数目。 增加最小副本可提供更大的池来处理传入峰值。

    若要增加最小副本数, 请`autoscale_min_replicas`将设置为较高的值。 可以使用以下代码来计算所需的副本, 并将值替换为项目特定的值:

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
    > 如果收到的请求高峰大于新的最小副本可以处理的数量, 则可能会再次收到503s。 例如, 当服务的流量增加时, 可能需要增加最小副本。

有关`autoscale_target_utilization`将、 `autoscale_max_replicas`和`autoscale_min_replicas`设置为的详细信息, 请参阅[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模块参考。


## <a name="advanced-debugging"></a>高级调试

在某些情况下, 可能需要以交互方式调试模型部署中包含的 Python 代码。 例如, 如果输入脚本失败, 原因无法由其他日志记录确定。 通过使用 Visual Studio Code 和针对 Visual Studio 的 Python 工具 (PTVSD), 你可以附加到在 Docker 容器中运行的代码。

> [!IMPORTANT]
> 当使用`Model.deploy()`和在本地部署模型时, `LocalWebservice.deploy_configuration`此调试方法不起作用。 相反, 必须使用[save-containerimage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)类创建映像。 
>
> 本地 web 服务部署需要在你的本地系统上进行工作的 Docker 安装。 在部署本地 web 服务之前, Docker 必须处于运行状态。 有关安装和使用 Docker 的信息, 请[https://www.docker.com/](https://www.docker.com/)参阅。

### <a name="configure-development-environment"></a>配置开发环境

1. 若要在本地 VS Code 开发环境中安装针对 Visual Studio 的 Python 工具 (PTVSD), 请使用以下命令:

    ```
    python -m pip install --upgrade ptvsd
    ```

    有关将 PTVSD 与 VS Code 一起使用的详细信息, 请参阅[远程调试](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)。

1. 若要配置 VS Code 以与 Docker 映像通信, 请创建新的调试配置:

    1. 在 VS Code 中, 选择 "__调试__" 菜单, 然后选择 "__打开配置__"。 将打开一个名为 "__启动__" 的文件。

    1. 在__启动 json__文件中, 找到包含`"configurations": [`的行, 然后在其后面插入以下文本:

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
        > 如果 "配置" 部分中已有其他项, 请在插入的代码后添加一个逗号 (,)。

        本部分使用端口5678附加到 Docker 容器。

    1. 保存__启动__文件。

### <a name="create-an-image-that-includes-ptvsd"></a>创建包含 PTVSD 的映像

1. 修改部署的 conda 环境, 使其包括 PTVSD。 下面的示例演示如何使用`pip_packages`参数添加该参数:

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

1. 若要在服务启动时启动 PTVSD 并等待连接, 请将以下内容添加到`score.py`文件顶部:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 在调试过程中, 你可能需要对映像中的文件进行更改, 而不必重新创建。 若要在 Docker 映像中安装文本编辑器 (vim), 请创建一个名为`Dockerfile.steps`的新文本文件, 并使用以下内容作为该文件的内容:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    使用文本编辑器, 可以修改 docker 映像中的文件, 以测试更改而无需创建新映像。

1. 若要创建使用该`Dockerfile.steps`文件的映像, 请在创建映像时`docker_file`使用参数。 下面的示例演示如何执行此操作:

    > [!NOTE]
    > 此示例假设`ws`指向 Azure 机器学习工作区, 这`model`是正在部署的模型。 此`myenv.yml`文件包含在步骤1中创建的 conda 依赖项。

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

创建映像后, 将显示注册表中的映像位置。 位置类似于以下文本:

```text
myregistry.azurecr.io/myimage:1
```

在此文本示例中, 注册表名称为`myregistry` , 映像命名`myimage`为。 映像版本是`1`。

### <a name="download-the-image"></a>下载映像

1. 打开命令提示符、终端或其他 shell, 并使用以下[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)命令向包含 Azure 机器学习工作区的 Azure 订阅进行身份验证:

    ```azurecli
    az login
    ```

1. 若要对包含映像的 Azure 容器注册表 (ACR) 进行身份验证, 请使用以下命令。 替换`myregistry`为注册映像时返回的:

    ```azurecli
    az acr login --name myregistry
    ```

1. 若要将映像下载到本地 Docker, 请使用以下命令。 替换`myimagepath`为注册映像时返回的位置:

    ```bash
    docker pull myimagepath
    ```

    图像路径应类似`myregistry.azurecr.io/myimage:1`于。 其中`myregistry`是你的注册表`myimage` , `1`是映像, 是映像版本。

    > [!TIP]
    > 上一步中的身份验证不是最后一步。 如果在身份验证命令和 pull 命令之间等待足够长的时间, 则会收到身份验证失败。 如果发生这种情况, 请重新验证。

    完成下载所用的时间取决于 internet 连接的速度。 在此过程中将显示下载状态。 下载完成后, 可以使用`docker images`命令验证是否已下载。

1. 若要更轻松地处理图像, 请使用以下命令添加标记。 替换`myimagepath`为步骤2中的位置值。

    ```bash
    docker tag myimagepath debug:1
    ```

    对于其余步骤, 可以引用本地映像`debug:1` , 而不是完整的映像路径值。

### <a name="debug-the-service"></a>调试服务

> [!TIP]
> 如果在`score.py`文件中设置 PTVSD 连接的超时值, 则必须在超时过期之前将 VS Code 连接到调试会话。 开始 VS Code, 打开的本地副本`score.py`, 设置一个断点, 并在使用本节中的步骤之前准备就绪。
>
> 有关调试和设置断点的详细信息, 请参阅[调试](https://code.visualstudio.com/Docs/editor/debugging)。

1. 若要使用映像启动 Docker 容器, 请使用以下命令:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 若要将 VS Code 附加到容器中的 PTVSD, 请打开 VS Code 并使用 F5 键或选择 "__调试__"。 出现提示时，请__选择 Azure 机器学习：Docker 调试__配置。 还可以从侧栏中选择 "调试" 图标， __Azure 机器学习："调试__ " 下拉菜单中的 Docker 调试条目, 然后使用绿色箭头附加调试器。

    !["调试" 图标、"启动调试" 按钮和配置选择器](media/how-to-troubleshoot-deployment/start-debugging.png)

此时, VS Code 连接到 Docker 容器内部的 PTVSD, 并在之前设置的断点处停止。 现在可以单步执行代码, 就像运行、查看变量等。

有关使用 VS Code 调试 Python 的详细信息, 请参阅[调试 python 代码](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)。

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>修改容器文件

若要对图像中的文件进行更改, 可以附加到正在运行的容器, 然后执行 bash shell。 在这里, 你可以使用 vim 编辑文件:

1. 若要连接到正在运行的容器并在容器中启动 bash shell, 请使用以下命令:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 若要查找服务使用的文件, 请在容器中使用 bash shell 中的以下命令:

    ```bash
    cd /var/azureml-app
    ```

    从这里, 你可以使用 vim 来编辑该`score.py`文件。 有关使用 vim 的详细信息, 请参阅[使用 vim 编辑器](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)。

1. 通常不会保留对容器所做的更改。 若要保存所做的任何更改, 请使用以下命令, 然后在退出前面步骤中 (即, 另一个 shell 中) 启动的 shell 之前:

    ```bash
    docker commit debug debug:2
    ```

    此命令创建一个名为`debug:2`的新映像, 其中包含您的编辑内容。

    > [!TIP]
    > 需要停止当前容器并开始使用新版本, 然后更改才会生效。

1. 请确保将对容器中的文件所做的更改与 VS Code 使用的本地文件保持同步。 否则, 调试器体验将不会按预期工作。

### <a name="stop-the-container"></a>停止容器

若要停止容器, 请使用以下命令:

```bash
docker stop debug
```

## <a name="next-steps"></a>后续步骤

详细了解部署：

* [部署方式和部署位置](how-to-deploy-and-where.md)
* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
