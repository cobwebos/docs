---
title: "为用于容器的 Azure Web 应用使用自定义 Docker 映像 | Microsoft Docs"
description: "如何对用于容器的 Azure Web 应用使用自定义 Docker 映像。"
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
ms.date: 09/03/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 760772d1d1c79dd4a1114c36971de0b3693ab74f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>为用于容器的 Azure Web 应用使用自定义 Docker 映像

在 Linux 上，[用于容器的 Web 应用](app-service-linux-intro.md)提供内置 Docker 映像，并支持特定版本，例如 PHP 7.0 和 Node.js 4.5。 用于容器的 Web 应用利用 Docker 容器技术，以“平台即服务”的方式同时托管内置映像和自定义映像。 本教程介绍如何生成在用于容器的 Web 应用上使用的自定义 Docker 映像，这在后列情况下是一种常见模式：缺少适用于所用语言的内置映像时，或当应用程序需要某种内置映像不提供的特定配置时。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [Git](https://git-scm.com/downloads)
* 一个有效的 [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)
* [Docker](https://docs.docker.com/get-started/#setup)
* 一个 [Docker 中心帐户](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机，然后更改保函示例代码的目录。

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>从 Docker 文件生成映像

下列 Docker 文件描述了运行应用程序所必需的 Python 环境。 此外，映像会设置一个 [SSH](https://www.ssh.com/ssh/protocol/) 服务器来实现容器和主机之间的安全通信。

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

要生成 Docker 映像，请运行 `docker build` 命令，并提供名称、`mydockerimage` 和标记 `v1.0.0`。 将 `<docker-id>` 替换为 Docker 中心帐户 ID。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

该命令生成的输出如下所示：

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

运行 Docker 容器来测试生成是否有效。 发布 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令并将映像的名称和标记传递给它。 还必须使用 `-p` 参数指定端口。

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

通过在本地浏览 Web 应用，验证 Web 应用和容器是否正常运行。

![在本地测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>将 Docker 映像推送到 Docker 中心

注册表是一种托管映像、提供服务映像和容器服务的应用程序。 要共享映像，必须先将其推送至注册表。 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> 推送至专用 Docker 注册表？ 请参阅有关如何[将 Docker 映像推送至专用注册表](#push-a-docker-image-to-private-registry-optional)的可选说明。

<!--## [Docker Hub](#tab/docker-hub)-->

Docker 中心是一种用于 Docker 映像的注册表，使用该注册表，用户能够托管自己的存储库，无论是公共的还是专用的均可。 要将自定义 Docker 映像推送至公共 Docker 中心，请使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令，并提供完整的映像名称和标记。 完整的映像名称和标记类似于下例所示：

```bash
<docker-id>/image-name:tag
```

如果尚未登录 Docker 中心，请首先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 登录，然后再尝试推送映像。

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

如果看到“登录成功”消息，说明登录成功。 登录后，即可使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送至 Docker 中心。

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

可通过检查命令的输出来验证推送是否成功。

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>为容器创建 Web 应用

可使用 Azure Web 应用在云中托管本机 Linux 应用程序。 要创建用于容器的 Web 应用，必须依次运行相应的 Azure CLI 命令来创建组、服务计划和 Web 应用本身。 首先运行 [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 命令，并传入位置和唯一的名称。

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

将显示类似于下面示例的输出：

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) 命令，凭借组的名称来帮助创建应用服务计划。 还需要为其赋予一个唯一名称，并设置 `--is-linux` 标记。

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

创建服务计划所生成的结果与下列示例相似：

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

在创建资源组和服务计划之后，即可运行 [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) 命令来创建 Web 应用。 请注意，运行时堆栈为 Python 3.4，且 Web 应用会使用先前步骤中所设置的资源组和服务计划。

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

用于创建 Web 应用的命令其生成的输出如下所示：

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

大部分 Web 应用都具有需要进行配置的应用程序设置。 如果使用的现有 Docker 映像是由其他人生成的，则映像可能要求为应用程序指定一个不是端口 80 的端口。 要设置 `WEBSITES_PORT`，请运行 [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings) 命令，如下方代码示例所示：

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> 应用程序设置区分大小写。
>

浏览该 Web 应用，以验证其能够正常运行。 请勿忘记端口号。

![测试 Web 应用端口配置](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>配置 Web 应用以从 Docker 中心使用 Docker 容器

[az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) 命令允许使用自定义 Docker 映像。

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> 从专用 Docker 注册表部署？ 请参阅有关如何[将 Web 应用配置为从专用注册表使用 Docker 容器](#configure-web-app-to-use-docker-container-from-a-private-registry-optional)的可选说明。

<!-- # [Docker Hub](#tab/docker-hub)-->

要将 Web 应用配置为使用公共 Docker 注册表，请将应用名称、资源组、映像名称和 URL 传递给 [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 命令。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

如果配置更改成功，会返回有关容器的常规信息。

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>在 Azure 中测试应用程序

测试之前，必须使用 [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) 重新启动 Web 应用，以使配置更改生效。

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

重新启动命令将在不进行任何提示的情况下重新启动 Web 应用，因此终端上将不显示任何反馈。 Web 应用运行后，请立即在 `http://<username>.azurewebsites.net` 处浏览 Web 应用的 URL 来测试应用。 验证应用是否显示新的欢迎消息。

![在 Azure 中测试 Web 应用](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>更改 Web 应用并重新部署

`using-custom-docker-image` 目录中会存在一个名为 `app.py` 的 Python 文件。 此文件包含显示 `Hello World!` 消息的代码行。 更改该代码行以使其显示消息 `Hello World of Web Apps running in Docker Containers!`。

```python
return "Hello World of Web Apps running in Docker Containers!"
```

当修改并保存 Python 文件后，必须重新生成并推送新的 Docker 映像。 为使更改生效，请重新启动 Web 应用。 使用先前在本教程中使用的命令。 可参阅[从 Docker 文件生成映像](#build-the-image-from-the-docker-file)和[推送 Docker 映像](#push-docker-image)部分。 按照[在 Azure 中测试应用程序](#tTest-the-application-in-azure)中的说明来测试 Web 应用

## <a name="connect-to-web-app-for-containers-using-ssh"></a>使用 SSH 连接到用于容器的 Web 应用

SSH 实现容器和客户端之间的安全通信。 要让自定义 Docker 映像支持 SHH，必须将其构建到 Dockerfile 中。 在 Docker 文件中启用 SSH。 已在示例 Dockerfile 中添加 SSH 指令，因此可使用这些指令来操作自己的自定义映像：

* [RUN](https://docs.docker.com/engine/reference/builder/#run) 指令调用 `apt-get`，然后将根帐户的密码设置为 `"Docker!"`。

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > 此配置不允许从外部建立到容器的连接。 只能通过 Kudu/SCM 站点使用 SSH。 Kudu/SCM 站点使用发布凭据进行身份验证。

* [COPY](https://docs.docker.com/engine/reference/builder/#copy) 指令指示 Docker 引擎将 [sshd_config](http://man.openbsd.org/sshd_config) 文件复制到 /etc/ssh/ 目录。 配置文件应当以 Azure-App-Service GitHub 存储库中的 [sshd_config 文件](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config)为基础。

    > [!NOTE]
    > sshd_config 文件必须包括以下项： 
    > * `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > * `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 指令公开容器中的端口 2222。 虽然根密码已知，但无法从 Internet 访问端口 2222。 它是一个仅供内部使用的端口，仅可供专用虚拟网络的桥网络中的容器访问。 此后，命令会复制 SSH 配置详细信息，并启动 `ssh` 服务。

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

示例代码中的 `init_container.sh` 文件包含如何在容器运行时对其进行初始化的相关说明。 Dockerfile 使用 [COPY](https://docs.docker.com/engine/reference/builder/#copy)、[RUN](https://docs.docker.com/engine/reference/builder/#run) 和 [CMD](https://docs.docker.com/engine/reference/builder/#cmd) 指令来正确启动 `init_container.sh` 脚本。

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

可能需要验证某些特定的应用程序正在容器中运行。 要检查容器并验证运行的进程，请首先打开浏览器，并导航至 `https://<app name>.scm.azurewebsites.net/webssh/host`。 接着会被重定向到显示交互式控制台的页面。 在命令提示符处发布 `top` 命令。

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

祝贺你！ 你已为用于容器的 Azure Web 应用配置自定义 Docker 映像。

## <a name="push-a-docker-image-to-private-registry-optional"></a>将 Docker 映像推送到专用注册表（可选）

Azure 容器注册表是 Azure 的一项托管 Docker 服务，用于托管专用映像。 其部署类型不限，包括 [Docker Swarm](https://docs.docker.com/engine/swarm/)、[Kubernetes](https://kubernetes.io/) 和 Azure 应用服务容器。 使用 Azure 容器注册表和使用其他任何专用注册表一样，因此，如果需要使用自己的专用注册表，那么完成此任务的步骤是相似的。

使用 [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) 命令创建 Azure 容器注册表。 为 SKU 传入名称、资源组和 `Basic`。 可用的 SKU 有 `Classic`、`Basic`、`Standard` 和 `Premium`。

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

创建一个容器将产生下列输出：

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
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

要向注册表推送一个映像，需要先提供凭据，以便注册表接受推送。 可使用 [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 命令来检索这些凭据。 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
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
  "username": "<azure-container-registry-name>"
}
```

现已拥有必需的凭据，可使用 `docker login` 命令登录到 Azure 容器注册表。 若要登录，需要服务器名称。 使用格式 `{azure-container-registry-name>.azurecr.io`。

```bash
docker login <azure-container-registry-name>.azurecr.io --username <azure-container-registry-name> --password <password> 
```

确认登录成功。 使用 `docker push` 命令推送映像，并使用注册表名称后跟映像名称和标记来标记映像。

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

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>配置 Web 应用以从专用注册表中使用 Docker 容器（可选）

可在 Linux 上配置 Web 应用，以便其运行存储在 Azure 容器注册表中的容器。 使用 Azure 容器注册表和使用其他任何专用注册表一样，因此，如果需要使用自己的专用注册表，那么完成此任务的步骤是相似的。

[az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) 命令显示容器注册表的密码。 复制用户名和其中一个密码，以便使用它们在下一步中配置 Web 应用。

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
  "username": "<azure-container-registry-name>"
}
```

运行 [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set) 命令。 此命令将自定义 Docker 映像分配给 Web 应用。 请注意，需要格式为 `https://{your-registry-username}.azurecr.io` 的 URL。 此外，要访问容器注册表，Web 应用还需要上一个步骤中获取的用户名和密码。

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> 注册表 URL 中须含有 `https`。
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
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
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

[用于容器的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
