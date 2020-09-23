---
title: 教程 - 使用 Docker Compose 部署多容器组
description: 使用 Docker Compose 构建并运行多容器应用程序，然后将该应用程序引入到 Azure 容器实例中
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: ''
ms.openlocfilehash: 1e8a5cd856358a0dc3e9c356cb3a55f75db29c86
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708241"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>教程：使用 Docker Compose 部署多容器组 

在本教程中，你使用 [Docker Compose](https://docs.docker.com/compose/) 在本地定义并运行多容器应用程序，然后在 Azure 容器实例中将其部署为[容器组](container-instances-container-groups.md)。 

使用 Docker 开发云原生应用时，如果希望从本地开发无缝切换到云部署，可以按需在 Azure 容器实例中运行容器。 此功能是通过 [Docker 与 Azure 之间的集成](https://docs.docker.com/engine/context/aci-integration/)启用的。 你可以使用原生 Docker 命令在 Azure 中运行[单容器实例](quickstart-docker-cli.md)或多容器组。

> [!IMPORTANT]
> 并非 Azure 容器实例的所有功能都受支持。 如需提供有关 Docker-Azure 集成的反馈，可在 [Docker ACI 集成](https://github.com/docker/aci-integration-beta) GitHub 存储库中创建问题。

> [!TIP]
> 可以使用[用于 Visual Studio Code 的 Docker 扩展](https://aka.ms/VSCodeDocker)来实现一种集成体验，以便开发、运行和管理容器、映像和上下文。

本文内容：

> [!div class="checklist"]
> * 创建 Azure 容器注册表
> * 克隆 GitHub 中的应用程序源代码
> * 使用 Docker Compose 在本地构建映像并运行多容器应用程序
> * 将应用程序映像推送到容器注册表
> * 为 Docker 创建 Azure 上下文
> * 启动 Azure 容器实例中的应用程序

## <a name="prerequisites"></a>系统必备

* **Azure CLI** - 必须已在本地计算机上安装了 Azure CLI。 建议使用 2.10.1 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

* **Docker Desktop** - 必须使用适用于 [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 或 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) 的 Docker Desktop 2.3.0.5 或更高版本。 或者安装[适用于 Linux 的 Docker ACI Integration CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)。

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>获取应用程序代码

本教程使用的示例应用程序是一个基本的投票应用。 该应用程序由前端 Web 组件和后端 Redis 实例组成。 Web 组件打包到自定义容器映像中。 Redis 实例使用 Docker 中心提供的未修改的映像。

使用 [git](https://git-scm.com/downloads) 可将示例应用程序克隆到开发环境：

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

切换到克隆目录。

```console
cd azure-voting-app-redis
```

目录内包含应用程序源代码和预创建的 Docker Compose 文件 docker-compose.yaml。

## <a name="modify-docker-compose-file"></a>修改 Docker Compose 文件

在文本编辑器中打开 docker-compose.yaml。 此文件配置 `azure-vote-back` 和 `azure-vote-front` 服务。

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

在 `azure-vote-front` 配置中，进行以下两项更改：

1. 更新 `azure-vote-front` 服务中的 `image` 属性。 用你的 Azure 容器注册表的登录服务器名称作为映像名称的前缀，即 \<acrName\>.azurecr.io。 例如，如果你的注册表名称为 myregistry，登录服务器名称为 myregistry.azurecr.io（全小写），则映像属性为 `myregistry.azurecr.io/azure-vote-front`。
1. 将 `ports` 映射更改为 `80:80`。 保存文件。

更新后的文件应类似于以下内容：

```yml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

进行这些替换后，你可以针对 Azure 容器注册表对将要在下一步生成的 `azure-vote-front` 映像进行标记，并且可以拉取该映像，以便在 Azure 容器实例中运行它。

> [!TIP]
> 对于此方案，无需使用 Azure 容器注册表。 例如，你可以选择 Docker Hub 内的一个专用存储库来承载应用程序映像。 如果选择其他注册表，请相应地更新映像属性。

## <a name="run-multi-container-application-locally"></a>在本地运行多容器应用程序

运行 [docker-compose up](https://docs.docker.com/compose/reference/up/)，它使用示例 `docker-compose.yaml` 文件来生成容器映像、下载 Redis 映像以及启动应用程序：

```console
docker-compose up --build -d
```

完成后，使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令查看创建的映像。 已下载或创建三个映像。 `azure-vote-front` 映像包含前端应用程序，后者使用 `uwsgi-nginx-flask` 映像作为基础。 `redis` 映像用于启动 Redis 实例。

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
redis                                     latest     a1b99da73d05        7 days ago          104MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

运行 [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) 命令，查看正在运行的容器：

```
$ docker ps

CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front  "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b68fed4b66b6        redis                                   "docker-entrypoint.s…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

若要查看正在运行的应用程序，请在本地 Web 浏览器中输入 `http://localhost:80`。 示例应用程序会加载，如以下示例所示：

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="投票应用的图像":::

尝试本地应用程序后，请运行 [docker-compose down](https://docs.docker.com/compose/reference/down/) 以停止应用程序并删除容器。

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>将映像推送到容器注册表

若要将应用程序部署到 Azure 容器实例，需要将 `azure-vote-front` 映像推送到容器注册表。 运行 [docker-compose push](https://docs.docker.com/compose/reference/push) 来推送映像：

```console
docker-compose push
```

推送到注册表可能需要几分钟时间。

若要验证映像是否存储在注册表中，请运行 [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) 命令：

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>将应用程序部署到 Azure 容器实例

接下来，切换到 ACI 上下文。 后续 Docker 命令在此上下文中运行。

```console
docker context use myacicontext
```

运行 `docker compose up` 以在 Azure 容器实例中启动应用程序。 将从你的容器注册表中拉取 `azure-vote-front` 映像，并在 Azure 容器实例中创建容器组。

```console
docker compose up
```

> [!NOTE]
> ACI 上下文中当前可用的 Docker Compose 命令为 `docker compose up` 和 `docker compose down`。 在这两个命令中，`docker` 和 `compose` 之间没有连字符。

容器组很快就会部署完毕。 示例输出：

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

运行 `docker ps`，查看正在运行的容器和分配给该容器组的 IP 地址。

```console
docker ps
```

示例输出：

```
CONTAINER ID                           IMAGE                                    COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    redis                                                        Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                       Running             52.179.23.131:80->80/tcp
```

若要查看云中正在运行的应用程序，请在本地 Web 浏览器中输入显示的 IP 地址。 在此示例中，输入 `52.179.23.131`。 示例应用程序会加载，如以下示例所示：

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="ACI 中的投票应用的图像":::

若要查看前端容器的日志，请运行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs) 命令。 例如：

```console
docker logs azurevotingappredis_azure-vote-front
```

你还可以使用 Azure 门户或其他 Azure 工具来查看部署的容器组的属性和状态。

完成应用程序试用后，通过 `docker compose down` 停止应用程序和容器：

```console
docker compose down
```

此命令删除 Azure 容器实例中的容器组。

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Docker Compose 从在本地运行多容器应用程序切换到在 Azure 容器实例中运行。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure 容器注册表
> * 克隆 GitHub 中的应用程序源代码
> * 使用 Docker Compose 在本地构建映像并运行多容器应用程序
> * 将应用程序映像推送到容器注册表
> * 为 Docker 创建 Azure 上下文
> * 启动 Azure 容器实例中的应用程序

还可以使用[用于 Visual Studio Code 的 Docker 扩展](https://aka.ms/VSCodeDocker)来实现一种集成体验，以便开发、运行和管理容器、映像和上下文。

若要利用 Azure 容器实例中的更多功能，请使用 Azure 工具指定一个多容器组。 有关示例，请参阅以下相关教程，了解如何通过 Azure CLI 使用 [YAML 文件](container-instances-multi-container-yaml.md)部署容器组，或使用 [Azure 资源管理器模板](container-instances-multi-container-group.md)进行部署。 