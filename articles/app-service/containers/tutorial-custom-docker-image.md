---
title: 为用于容器的 Web 应用生成自定义映像 - Azure 应用服务 | Microsoft Docs
description: 如何对用于容器的 Web 应用使用自定义 Docker 映像。
keywords: azure 应用服务、web 应用、linux、docker、容器
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8463ffcb9d9983ff435c01f75dd48f68bde31767
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545596"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>教程：从专用注册表生成自定义映像并在应用服务中运行

在 Linux 上，[应用服务](app-service-linux-intro.md)提供内置的 Docker 映像，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 应用服务使用 Docker 容器技术，以“平台即服务”的方式同时托管内置映像和自定义映像。 本教程介绍如何生成自定义映像并在应用服务中运行它。 此模式适用于内置的映像不包括所选语言的情况，或者应用程序需要的特定配置未在内置映像中提供的情况。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将自定义映像部署到专用容器注册表
> * 在应用服务中运行自定义映像
> * 配置环境变量
> * 更新并重新部署映像
> * 访问诊断日志
> * 使用 SSH 连接到容器

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机，然后切换至包含示例代码的目录。

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>从 Docker 文件生成映像

在 Git 存储库中查看 _Dockerfile_。 此文件描述了运行应用程序所需的 Python 环境。 此外，映像会设置一个 [SSH](https://www.ssh.com/ssh/protocol/) 服务器来实现容器和主机之间的安全通信。

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

使用 `docker build` 命令生成 Docker 映像。

```bash
docker build --tag mydockerimage .
```

运行 Docker 容器来测试生成是否有效。 发出 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 命令并将映像的名称和标记传递给它。 确保使用 `-p` 参数指定端口。

```bash
docker run -p 8000:8000 mydockerimage
```

浏览到 `http://localhost:8000`，验证 Web 应用和容器是否正常运行。

![在本地测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

若要创建使用刚刚创建的映像的应用，请运行 Azure CLI 命令来创建资源组，推送映像，然后创建应用服务计划 Web 应用来运行该映像。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

在 Cloud Shell 中使用 [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) 命令创建 Azure 容器注册表。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>登录到 Azure 容器注册表

若要将映像推送至注册表，需要通过专用注册表的身份验证。 在 Cloud Shell 中，使用 [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) 命令从自己创建的注册表中检索凭据。

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

此输出显示两个密码以及用户名。

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

在本地终端窗口中，使用 `docker login` 命令登录到 Azure 容器注册表，如下面的示例中所示。 将 \<azure-container-registry-name> 和 \<registry-username> 替换为自己的注册表值。 出现提示时，键入在上一步骤中获取的某个密码。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

确认登录成功。

### <a name="push-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

为 Azure 容器注册表标记逻辑映像。 例如：
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

使用 `docker push` 命令推送映像。 使用注册表名称后跟映像名称和标记的方式标记映像。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

返回 Cloud Shell，验证推送是否成功。

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

应该会看到以下输出。

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](app-service-linux-intro.md)。 将 \<app-name> 替换为唯一的应用名称，并将 \<azure-container-registry-name> 替换为自己的注册表名称。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>在 Web 应用中配置注册表凭据

要让应用服务拉取专用映像，需要提供与注册表和映像有关的信息。 在 Cloud Shell 中，请使用 [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 命令提供这些信息。 替换 \<app-name>、\<azure-container-registry-name>、\<registry-username> 和 \<password>。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> 在使用注册表而不是 Docker 中心时，`--docker-registry-server-url` 必须采用 `https://` 格式，后接注册表的完全限定域名。
>

### <a name="configure-environment-variables"></a>配置环境变量

大多数 Docker 映像都使用自定义环境变量，例如 80 以外的端口。 可以通过 `WEBSITES_PORT` 应用设置将映像使用的端口告知应用服务。 [本教程中的 Python 示例](https://github.com/Azure-Samples/docker-django-webapp-linux)的 GitHub 页显示，需将 `WEBSITES_PORT` 设置为 _8000_。

若要设置应用设置，请在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令。 应用设置区分大小写，用空格分开。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>测试 Web 应用

浏览到 Web 应用 (`http://<app-name>.azurewebsites.net`)，验证其能否正常运行。

> [!NOTE]
> 首次访问应用时，可能会需要一些时间来让应用服务拉取整个映像。 如果浏览器超时，刷新页面即可。

![测试 Web 应用端口配置](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>更改 Web 应用并重新部署

在本地 Git 存储库中打开 app/templates/app/index.html。 找到第一个 HTML 元素，将其更改为 。

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

当修改并保存 Python 文件后，必须重新生成并推送新的 Docker 映像。 为使更改生效，请重新启动 Web 应用。 使用先前在本教程中使用的命令。 可参阅[从 Docker 文件生成映像](#build-the-image-from-the-docker-file)和[向 Azure 容器注册表推送映像](#push-image-to-azure-container-registry)。 按照[测试 Web 应用](#test-the-web-app)中的说明测试 Web 应用

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>启用 SSH 连接

SSH 实现容器和客户端之间的安全通信。 若要启用到容器的 SSH 连接，必须针对该连接配置自定义映像。 我们来看看已具备所需配置的示例存储库。

* 在 [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile) 中，以下代码用于安装 SSH 服务器和设置登录凭据。

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > 此配置不允许从外部建立到容器的连接。 只能通过 Kudu/SCM 站点使用 SSH。 Kudu/SCM 站点使用 Azure 帐户进行身份验证。

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) 可将 [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config file in the repository) 复制到“/etc/ssh/”存储库。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) 公开容器中的端口 2222。 它是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [入口脚本](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5)启动 SSH 服务器。

      ```bash
      #!/bin/bash
      service ssh start
    ```

### Open SSH connection to container

SSH connection is available only through the Kudu site, which is accessible at `https://<app-name>.scm.azurewebsites.net`.

To connect, browse to `https://<app-name>.scm.azurewebsites.net/webssh/host` and sign in with your Azure account.

You are then redirected to a page displaying an interactive console.

You may wish to verify that certain applications are running in the container. To inspect the container and verify running processes, issue the `top` command at the prompt.

```bash
top
```

`top` 命令会公开所有正在容器中运行的进程。

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

祝贺你！ 你已在应用服务中配置自定义 Linux 容器。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 将自定义映像部署到专用容器注册表
> * 在应用服务中运行自定义映像
> * 配置环境变量
> * 更新并重新部署映像
> * 访问诊断日志
> * 使用 SSH 连接到容器

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置自定义容器](configure-custom-container.md)

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)
