---
title: 包模型
titleSuffix: Azure Machine Learning
description: 将模型打包为 Dockerfile
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: edaae4f4c06f038d12267e35a2c356af430e1555
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998825"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>如何使用 Docker 打包注册的模型

本文介绍如何使用 Docker 打包已注册的 Azure 机器学习模型。

## <a name="prerequisites"></a>必备条件

本文假设你已在机器学习工作区中训练并注册了一个模型。 若要了解如何训练和注册 scikit-learn 模型，请[遵循本教程](how-to-train-scikit-learn.md)。


## <a name="package-models"></a>包模型

在某些情况下，可能需要在不部署模型的情况下创建 Docker 映像（例如要[部署到 Azure 应用服务](how-to-deploy-app-service.md)）。 或者，你可能希望下载映像并在本地 Docker 安装上运行它。 甚至可能需要下载用于生成映像的文件、对其进行检查和修改并手动生成映像。

这些工作都可以通过打包模型来完成。 此方法能对将模型作为 Web 服务托管所需的全部资产进行打包，让你能下载完整生成的 Docker 映像或生成该映像所需的文件。 可以通过两种方式使用模型打包：

**下载已打包的模型：** 下载包含模型以及将其作为 Web 服务托管所需的其他文件的 Docker 映像。

**生成 Dockerfile：** 下载生成 Docker 映像所需的 Dockerfile、模型、入口脚本和其他资产。 然后可以先检查这些文件或进行修改，再在本地生成映像。

这两个包都可用于获取本地 Docker 映像。

> [!TIP]
> 创建包类似于部署模型。 使用注册的模型和推理配置。

> [!IMPORTANT]
> 若要下载完整生成的映像或要在本地生成映像，需要在开发环境中安装 [Docker](https://www.docker.com)。

### <a name="download-a-packaged-model"></a>下载已打包的模型

下面的示例生成一个映像，该映像已在工作区的 Azure 容器注册表中注册：

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

创建包后，可以使用 `package.pull()` 将映像拉取到本地 Docker 环境。 此命令的输出将显示映像的名称。 例如： 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

下载模型后，使用 `docker images` 命令列出本地映像：

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

若要启动基于此映像的本地容器，请使用以下命令从 Shell 或命令行启动已命名容器。 使用 `docker images` 命令返回的映像 ID 替换 `<imageid>` 值。

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

此命令启动名为 `myimage` 的映像的最新版本。 它将本地端口 6789 映射到 Web 服务正在侦听的容器中的端口 (5001)。 还将名称 `mycontainer` 分配给容器，使容器更易于停止。 启动容器后，可以将请求提交到 `http://localhost:6789/score`。

### <a name="generate-a-dockerfile-and-dependencies"></a>生成 Dockerfile 和依赖项

下面的示例演示如何下载在本地生成映像所需的 Dockerfile、模型和其他资产。 `generate_dockerfile=True` 参数表示需要的是文件，而不是完整生成的映像。

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

此代码将生成映像所需的文件下载到 `imagefiles` 目录。 已保存的文件中包含的 Dockerfile 引用存储在 Azure 容器注册表中的基础映像。 在本地 Docker 安装上生成映像时，需要使用地址、用户名和密码完成注册表身份验证。 采取以下步骤，通过本地 Docker 安装生成映像：

1. 在 Shell 或命令行会话中使用以下命令，使用 Azure 容器注册表对 Docker 进行身份验证。 将 `<address>`、`<username>` 和 `<password>` 替换为 `package.get_container_registry()` 检索到的值。

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 若要生成映像，请使用以下命令。 将 `<imagefiles>` 替换为 `package.save()` 保存文件的目录的路径。

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    此命令将映像名设置为 `myimage`。

若要验证是否已生成映像，请使用 `docker images` 命令。 在列表中应该能看到 `myimage` 映像：

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

若要启动基于此映像的新容器，请使用以下命令：

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

此命令启动名为 `myimage` 的映像的最新版本。 它将本地端口 6789 映射到 Web 服务正在侦听的容器中的端口 (5001)。 还将名称 `mycontainer` 分配给容器，使容器更易于停止。 启动容器后，可以将请求提交到 `http://localhost:6789/score`。

### <a name="example-client-to-test-the-local-container"></a>测试本地容器的示例客户端

以下代码是可与容器结合使用的 Python 客户端示例：

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

若要通过示例了解采用其他编程语言的客户端，请参阅[使用部署为 Web 服务的模型](how-to-consume-web-service.md)。

### <a name="stop-the-docker-container"></a>停止 Docker 容器

若要停止容器，请在不同的 Shell 或命令行中使用以下命令：

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>后续步骤

* [排查部署失败的问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)
