---
title: Azure 机器学习服务部署故障排除指南
description: 了解如何使用 Azure 机器学习服务规避、解决和排除常见 Docker 部署错误。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 10/01/2018
ms.openlocfilehash: a10b05e95fa719b80775191e48bd4117e3a785fd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321676"
---
# <a name="troubleshooting-azure-machine-learning-service-deployments"></a>Azure 机器学习服务部署故障排除

本文将介绍如何使用 Azure 机器学习服务规避或解决常见 Docker 部署错误。

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

如果使用 `Webservice.deploy` API 或 `Webservice.deploy_from_model` API，该操作特别有用，因为这些功能可将上述步骤合并成单个操作。 通常这些 API 非常方便，但在通过将其替换为下述 API 调用进行故障排除时，分解步骤非常有用。

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
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
此映像日志 URI 是指向 Azure blob 存储中存储的日志文件的 SAS URL. 只需复制 URI 并将其粘贴到浏览器窗口，即可下载和查看日志文件。


## <a name="service-launch-fails"></a>服务启动失败
成功生成映像后，系统会尝试启动 ACI 或 AKS 中的容器，具体取决于你的部署配置。 通常建议先尝试 ACI 部署，因为它是更简单的单容器部署。 通过这种方式，可以排除任何特定于 AKS 的问题。

作为容器启动过程的一部分，评分脚本中的 `init()` 函数由系统调用。 如果 `init()` 函数中存在未捕获的异常，则可能在错误消息中看到 CrashLoopBackOff 错误。 以下是一些有助于解该问题的提示。

### <a name="inspect-the-docker-log"></a>检查 Docker 日志
可以通过服务对象打印详细的 Docker 引擎日志消息。

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>本地调试 Docker 映像
有时 Docker 日志体现的错误相关信息并不充足。 可以进一步操作并提取生成的 Docker 映像，启动本地容器并以交互方式直接在实时容器内进行调试。 若要启动本地容器，必须具有本地运行的 Docker 映像，此外，如果还安装有 [azure-cli](/cli/azure/install-azure-cli?view=azure-cli-latest)，此步骤将容易得多。

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
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

这将打印容器中的本地路径（相对于 `/var/azureml-app`），其中评分脚本有望找到模型文件或文件夹。 然后可以验证文件或文件夹是否确实位于其应该在的位置。


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
注意：通过 `run(input_data)` 调用返回错误消息应仅用于调试目的。 出于安全原因，在生产环境中执行此操作可能并非上策。


## <a name="next-steps"></a>后续步骤

详细了解部署： 
* [如何部署到 ACI](how-to-deploy-to-aci.md)

* [如何部署到 AKS](how-to-deploy-to-aks.md)

* [教程第 1 部分：培训模型](tutorial-train-models-with-aml.md)

* [教程第 2 部分：部署模型](tutorial-deploy-models-with-aml.md)
