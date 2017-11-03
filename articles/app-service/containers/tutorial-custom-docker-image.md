---
title: "为用于容器的 Web 应用使用自定义 Docker 映像 - Azure | Microsoft Docs"
description: "如何对用于容器的 Web 应用使用自定义 Docker 映像。"
keywords: "azure 应用服务、web 应用、linux、docker、容器"
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 8660bd09ea09e2c4c81da9c3ef66a1a448d3db43
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>对用于容器的 Web 应用使用自定义 Docker 映像

在 Linux 上，[用于容器的 Web 应用](app-service-linux-intro.md)提供内置 Docker 映像，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 用于容器的 Web 应用使用 Docker 容器技术，以“平台即服务”的方式同时托管内置映像和自定义映像。 本教程介绍如何生成自定义 Docker 映像并将其部署到用于容器的 Web 应用。 此模式适用于内置的映像不包括所选语言的情况，或者应用程序需要的特定配置未在内置映像中提供的情况。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [Git](https://git-scm.com/downloads)
* 一个有效的 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* 一个 [Docker 中心帐户](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机，然后切换至包含示例代码的目录。

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>从 Docker 文件生成映像

在 Git 存储库中查看 _Dockerfile_。 此文件描述了运行应用程序所需的 Python 环境。 此外，映像会设置一个 [SSH](https://www.ssh.com/ssh/protocol/) 服务器来实现容器和主机之间的安全通信。

```docker
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

要生成 Docker 映像，请运行 `docker build` 命令，并提供名称、`mydockerimage` 和标记 `v1.0.0`。 将 `<docker-id>` 替换为 Docker 中心帐户 ID。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

该命令生成的输出如下所示：

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

运行 Docker 容器来测试生成是否有效。 发布 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令并将映像的名称和标记传递给它。 确保使用 `-p` 参数指定端口。

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

浏览到 `http://localhost:2222`，验证 Web 应用和容器是否正常运行。

![在本地测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>将 Docker 映像推送到 Docker 中心

注册表是一种托管映像、提供服务映像和容器服务的应用程序。 要共享映像，必须先将其推送至注册表。 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> 推送至专用 Docker 注册表？ 请参阅有关如何[将 Docker 映像推送至专用注册表](#push-a-docker-image-to-private-registry-optional)的可选说明。

<!--## [Docker Hub](#tab/docker-hub)-->

Docker 中心是一种用于 Docker 映像的注册表，使用该注册表，用户能够托管自己的存储库，无论是公共的还是专用的均可。 要将自定义 Docker 映像推送至公共 Docker 中心，请使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令，并提供完整的映像名称和标记。 完整的映像名称和标记类似于下例所示：

```bash
<docker-id>/image-name:tag
```

如果尚未登录 Docker 中心，则请登录，方法是在尝试推送映像之前使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令。

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

如果看到“登录成功”消息，说明登录成功。 登录后，即可使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送至 Docker 中心。

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

可通过检查命令的输出来验证推送是否成功。

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>将应用部署到 Azure

可使用 Azure Web 应用在云中托管本机 Linux 应用程序。 要创建用于容器的 Web 应用，必须依次运行用来创建组的 Azure CLI 命令、服务计划和 Web 应用本身。 

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [az webapp create](/cli/azure/webapp#create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](app-service-linux-intro.md)。 不要忘记将 `<app_name>` 替换为唯一的应用名称，将 <docker-ID> 替换为 Docker ID。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
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
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>配置环境变量

大多数 Docker 映像的环境变量需要配置。 如果使用的现有 Docker 映像是由其他人生成的，该映像可能使用 80 之外的端口。 可以通过 `WEBSITES_PORT` 应用设置将映像使用的端口告知 Azure。 [本教程中的 Python 示例](https://github.com/Azure-Samples/docker-django-webapp-linux)的 GitHub 页显示，需将 `WEBSITES_PORT` 设置为 _8000_。

要设置应用设置，请使用 Cloud Shell 中的 [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update) 命令。 应用设置区分大小写，用空格分开。

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> 从专用 Docker 注册表部署？ 请参阅有关如何[将 Web 应用配置为使用专用注册表中的 Docker 容器](#configure-web-app-to-use-docker-container-from-a-private-registry-optional)的可选说明。

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>测试 Web 应用

浏览到 Web 应用 (`http://<app_name>azurewebsites.net`)，验证其能否正常运行。 

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

当修改并保存 Python 文件后，必须重新生成并推送新的 Docker 映像。 为使更改生效，请重新启动 Web 应用。 使用先前在本教程中使用的命令。 可参阅[从 Docker 文件生成映像](#build-the-image-from-the-docker-file)和[推送 Docker 映像到 Docker 中心](#push-the-docker-image-to-docker-hub)。 按照[测试 Web 应用](#test-the-web-app)中的说明测试 Web 应用

## <a name="connect-to-web-app-for-containers-using-ssh"></a>使用 SSH 连接到用于容器的 Web 应用

SSH 实现容器和客户端之间的安全通信。 要让自定义 Docker 映像支持 SHH，必须将其构建到 Dockerfile 中。 在 Docker 文件中启用 SSH。 SSH 指令已添加到示例 Dockerfile 中，因此可使用这些指令来操作自己的自定义映像：

* [RUN](https://docs.docker.com/engine/reference/builder/#run) 指令调用 `apt-get`，然后将根帐户的密码设置为 `"Docker!"`。

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > 此配置不允许从外部建立到容器的连接。 只能通过 Kudu/SCM 站点使用 SSH。 Kudu/SCM 站点使用发布凭据进行身份验证。

* [COPY](https://docs.docker.com/engine/reference/builder/#copy) 指令指示 Docker 引擎将 [sshd_config](http://man.openbsd.org/sshd_config) 文件复制到 /etc/ssh/ 目录。 配置文件应基于[此 sshd_config 文件](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config)。

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 文件必须包括以下项： 
    > * `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > * `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 指令公开容器中的端口 2222。 虽然根密码已知，但无法从 Internet 访问端口 2222。 它是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。 此后，命令会复制 SSH 配置详细信息，并启动 `ssh` 服务。

    ```docker
    EXPOSE 8000 2222

    RUN service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>打开到容器的 SSH 连接

用于容器的 Web 应用不允许从外部建立到容器的连接。 只能通过 Kudu 站点（可以通过 `https://<app_name>.scm.azurewebsites.net` 访问）使用 SSH。

若要进行连接，请浏览到 `https://<app_name>.scm.azurewebsites.net/webssh/host` 并使用 Azure 帐户登录。

接着会被重定向到显示交互式控制台的页面。 

可能需要验证某些特定的应用程序正在容器中运行。 若要检查容器并验证正在运行的进程，请在系统提示时发出 `top` 命令。

```bash
top
```

`top` 命令会公开所有正在容器中运行的进程。

```bash
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

祝贺你！ 你已为用于容器的 Web 应用配置自定义 Docker 映像。

## <a name="use-a-private-image-from-docker-hub-optional"></a>使用 Docker 中心的专用映像（可选）

在“创建 Web 应用”中，使用 `az webapp create` 命令指定 Docker 中心的映像。[](#create-a-web-app) 这完全适用于公共映像。 若要使用专用映像，需在 Azure Web 应用中配置 Docker 帐户 ID 和密码。

在 Cloud Shell 中的 `az webapp create` 命令后附加 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)。 替换 \<app_name>，并使用 Docker ID 和密码替换 _<docker-id>_ 和 _<password>_。

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

此命令显示的输出与下列 JSON 字符串类似，说明配置更改已成功：

```bash
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>使用任何专用注册表中的 Docker 映像（可选）

本部分介绍如何在用于容器的 Web 应用中使用专用注册表中的 Docker 映像，以 Azure 容器注册表为例。 使用其他专用注册表的步骤是类似的。 

Azure 容器注册表是 Azure 的一项托管 Docker 服务，用于托管专用映像。 其部署类型不限，包括 [Docker Swarm](https://docs.docker.com/engine/swarm/)、[Kubernetes](https://kubernetes.io/) 和用于容器的 Web 应用。 

### <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

在 Cloud Shell 中使用 [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) 命令创建 Azure 容器注册表。 为 SKU 传入名称、资源组和 `Basic`。 可用的 SKU 有 `Classic`、`Basic`、`Standard` 和 `Premium`。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

创建容器的操作将产生下列输出：

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>登录到 Azure 容器注册表

要向注册表推送一个映像，需要先提供凭据，以便注册表接受推送。 可以在 Cloud Shell 中使用 [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 命令检索这些凭据。 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

此命令显示两个密码，均可与用户名配合使用。

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

在本地终端窗口中，使用 `docker login` 命令登录到 Azure 容器注册表。 若要登录，需要服务器名称。 使用格式 `{azure-container-registry-name>.azurecr.io`。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

确认登录成功。 

### <a name="push-an-image-to-azure-container-registry"></a>向 Azure 容器注册表推送映像

使用 `docker push` 命令推送映像。 使用注册表名称后跟映像名称和标记的方式标记映像。

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

通过列出 ACR 存储库，验证推送是否已成功将容器添加到注册表。 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

列出注册表中的映像，确认 `mydockerimage` 在注册表中。

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>将 Web 应用配置为使用 Azure 容器注册表（或任何专用注册表）中的映像

可以配置用于容器的 Web 应用，使其运行存储在 Azure 容器注册表中的容器。 使用 Azure 容器注册表和使用任何专用注册表一样，因此，如果需要使用自己的专用注册表，则完成此任务的步骤是相似的。

在 Cloud Shell 中运行 [az acr credential show](/cli/azure/acr/credential#az_acr_credential_show)，显示 Azure 容器注册表的用户名和密码。 复制用户名和其中一个密码，以便使用它们在下一步中配置 Web 应用。

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

在 Cloud Shell 中运行 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) 命令，将自定义 Docker 映像分配到 Web 应用。 替换 \<app_name>、\<docker-registry-server-url>、_<registry-username>_ 和 _<password>_。 对于 Azure 容器注册表，\<docker-registry-server-url> 采用 `https://<azure-container-registry-name>.azurecr.io` 格式。 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> 在 \<docker-registry-server-url> 中，`https://` 是必需的。
>

此命令显示的输出与下列 JSON 字符串类似，说明配置更改已成功：

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

[Linux 上的 Azure 应用服务常见问题解答](app-service-linux-faq.md)
