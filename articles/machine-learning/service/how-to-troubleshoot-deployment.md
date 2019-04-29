---
title: 部署故障排除指南
titleSuffix: Azure Machine Learning service
description: 了解如何解决和解决，并进行故障排除与 AKS 和 ACI 使用 Azure 机器学习服务的常见 Docker 部署错误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817420"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Azure 机器学习服务 AKS 和 ACI 部署故障排除

本文将介绍如何使用 Azure 机器学习服务规避或解决 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS) 的常见 Docker 部署错误。

在 Azure 机器学习服务中部署模型时，系统将执行大量任务。 这是一系列复杂事件，有时会出现问题。 部署任务包括：

1. 在工作区模型注册表中注册模型。

2. 生成 Docker 映像，包括：
    1. 从注册表中下载已注册的模型。 
    2. 使用基于在环境 yaml 文件中指定的依赖项的 Python 环境创建 dockerfile。
    3. 添加在 dockerfile 中提供的模型文件和评分脚本。
    4. 使用 dockerfile 生成新的 Docker 映像。
    5. 向与工作区关联的 Azure 容器注册表注册 Docker 映像。

3. 将 Docker 映像部署到 Azure 容器实例 (ACI) 服务或 Azure Kubernetes 服务 (AKS)。

4. 在 ACI 或 AKS 中启动一个（或多个）新容器。 

请参阅[模型管理](concept-model-management-and-deployment.md)简介，详细了解此过程。

## <a name="before-you-begin"></a>开始之前

如果遇到任何问题，首先需要将部署任务（上述）分解为单独的步骤，以查出问题所在。 

此功能非常有用，如果使用的`Webservice.deploy`API，或`Webservice.deploy_from_model`API，因为这些功能组合在一起的上述步骤转换为单一操作。 通常这些 Api 非常方便，但它可帮助分解步骤，通过将它们替换为进行故障排除时以下 API 调用。

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
                                                      execution_script="score.py",
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
如果系统无法生成 Docker 映像，`image.wait_for_creation()` 调用将失败，并显示可提供部分线索的错误消息。 还可以从图像生成日志中找到错误的更多详细信息。 下面是一些显示如何发现映像生成日志 URI 的代码示例。

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

图像生成也可能因 Azure 密钥保管库上的访问策略的问题。 这可以使用 Azure 资源管理器模板创建工作区和关联的资源 （包括 Azure 密钥保管库），多个时间时发生。 例如，使用模板多次使用相同的参数作为持续集成和部署管道的一部分。

通过模板的大多数资源创建操作是幂等的但密钥保管库中清除的访问策略每次使用模板。 这将中断访问到密钥保管库的任何现有的工作区与正在使用它。 这会导致错误时尝试创建新的映像。 可以收到的错误的示例如下：

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
* 检查密钥保管库访问策略和用于设置`accessPolicies`模板的属性。
* 检查是否已存在的密钥保管库资源。 如果是这样，不重新创建它通过模板。 例如，添加一个参数，可用于禁用创建密钥保管库资源，如果它已存在。

## <a name="service-launch-fails"></a>服务启动失败
成功生成映像后，系统会尝试启动 ACI 或 AKS 中的容器，具体取决于你的部署配置。 建议先尝试使用 ACI 部署，因为它是更简单的单容器部署。 通过这种方式，可以排除任何特定于 AKS 的问题。

作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。 以下是一些有助于解该问题的提示。

### <a name="inspect-the-docker-log"></a>检查 Docker 日志
可以通过服务对象打印详细的 Docker 引擎日志消息。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>本地调试 Docker 映像
有时 Docker 日志体现的错误相关信息并不充足。 可以进一步操作并提取生成的 Docker 映像，启动本地容器并以交互方式直接在实时容器内进行调试。 若要启动本地容器，必须具有本地运行的 Docker 映像，此外，如果还安装有 [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，此步骤将容易得多。

首先需要找到映像位置：

```python
# print image location
print(image.image_location)
```

映像位置采用以下格式：`<acr-name>.azurecr.io/<image-name>:<version-number>`，如 `myworkpaceacr.azurecr.io/myimage:3`。 

现在，转到命令行窗口。 如果已安装 azure-cli，可以键入以下命令以登录到与存储映像的工作区关联的 ACR（Azure 容器注册表）。 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
如果没有安装 azure-cli，则可使用 `docker login` 命令登录 ACR。 但首先需要从 Azure 门户中检索 ACR 的用户名和密码。

登录 ACR 后，即可提取 Docker 映像并在本地启动容器，然后使用 `docker run` 命令启动 bash 会话，进行调试：

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

针对正在运行的容器启动 bash 会话后，可在 `/var/azureml-app` 文件夹中找到评分脚本。 然后可以启动 Python 会话调试评分脚本。 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
如果需要文本编辑器修改脚本，可安装 vim、nano、Emacs 或其他常用编辑器。

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

还可以在本地启动 Web 服务，并向其发送 HTTP 流量。 Docker 容器中的 Flask 服务器在端口 5001 上运行。 可映射到主机上可用的任何其他端口。
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>函数故障：get_model_path()
通常情况下，在评分脚本的 `init()` 函数中，会调用 `Model.get_model_path()` 函数来定位容器中的模型文件或模型文件的文件夹。 如果无法找到模型文件或文件夹，则通常会导致故障。 调试此错误的最简单方法是在容器 shell 中运行以下 Python 代码：

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

这将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。

将日志记录级别设置为 DEBUG 可能会导致记录其他信息，这可能有助于识别故障。

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
**注意**：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，在生产环境中执行此操作可能并非上策。

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

有关详细信息设置`autoscale_target_utilization`， `autoscale_max_replicas`，并`autoscale_min_replicas`有关，请参阅[AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py)模块引用。


## <a name="next-steps"></a>后续步骤

详细了解部署： 
* [部署方式和部署位置](how-to-deploy-and-where.md)

* [教程：训练和部署模型](tutorial-train-models-with-aml.md)
