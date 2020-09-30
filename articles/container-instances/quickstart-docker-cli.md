---
title: 快速入门 - 将 Docker 容器部署到容器实例 - Docker CLI
description: 本快速入门将使用 Docker CLI 快速部署在隔离的 Azure 容器实例中运行的容器化 Web 应用
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262308"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>快速入门：使用 Docker CLI 在 Azure 中部署容器实例

使用 Azure 容器实例在 Azure 中快速方便地运行无服务器 Docker 容器。 开发云原生应用时，如果希望从本地部署无缝切换到云部署，可以按需部署到容器实例。

本快速入门介绍如何使用本机 Docker CLI 命令部署 Docker 容器，并使其应用程序在 Azure 容器实例中可用。 此功能是通过 [Docker 与 Azure 之间的集成](https://docs.docker.com/engine/context/aci-integration/)启用的。 在执行 `docker run` 部署命令几秒钟之后，可以浏览到正在容器中运行的应用程序：

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="在浏览器中显示的使用 Azure 容器实例部署的应用":::

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][azure-account]。

对于本快速入门，你需要安装适用于 [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 或 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) 的 Docker Desktop 2.3.0.5 或更高版本。 或者安装[适用于 Linux 的 Docker ACI Integration CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)。 

> [!IMPORTANT]
> 并非 Azure 容器实例的所有功能都受支持。 如需提供有关 Docker 与 Azure 集成的反馈，可在 [aci-integration-beta](https://github.com/docker/aci-integration-beta) GitHub 存储库中创建问题。

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>创建容器

创建 Docker 上下文后，可以在 Azure 中创建容器。 本快速入门将使用公共 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像。 此映像打包了一个用 Node.js 编写的小型 Web 应用程序，该应用程序提供静态 HTML 页面。

首先，切换到 ACI 上下文。 所有后续 Docker 命令都在此上下文中运行。

```
docker context use myacicontext
```

运行以下 `docker run` 命令，通过向 Internet 公开的端口 80 创建 Azure 容器实例：

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

成功部署的示例输出：

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

运行 `docker ps` 以获取有关正在运行的容器的详细信息，包括公共 IP 地址：

```
docker ps
```


示例输出显示公共 IP 地址，在本例中为 52.230.225.232：

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 现在，在浏览器中转到该 IP 地址。 如果看到类似于下图的网页，那么恭喜你！ 现已成功将 Docker 容器中运行的应用程序部署到 Azure。

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="在浏览器中显示的使用 Azure 容器实例部署的应用":::

## <a name="pull-the-container-logs"></a>拉取容器日志

当需要对容器或它运行的应用程序进行故障排除时（或者只是查看其输出），请首先查看容器实例的日志。

例如，运行 `docker logs` 命令以查看 ACI 上下文中 hungry-kirch 容器的日志：

```azurecli-interactive
docker logs hungry-kirch
```

此输出显示容器的日志，并应显示在浏览器中查看应用程序时生成的 HTTP GET 请求。

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>清理资源

对容器完成操作后，运行 `docker rm` 将其删除。 此命令停止并删除 Azure 容器实例。

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Docker 与 Azure 之间的集成从公共映像中创建了 Azure 容器实例。 可以在 [Docker 文档](https://docs.docker.com/engine/context/aci-integration/)中详细了解集成方案。 

还可以使用[用于 Visual Studio Code 的 Docker 扩展](https://aka.ms/VSCodeDocker)来实现一种集成体验，以便开发、运行和管理容器、映像和上下文。

若要使用 Azure 工具创建和管理容器实例，请参阅使用 [Azure CLI](container-instances-quickstart.md)、[Azure PowerShell](container-instances-quickstart-powershell.md)、[Azure 门户](container-instances-quickstart-portal.md)和 [Azure 资源管理器](container-instances-quickstart-template.md)的其他快速入门。

如果你希望使用 Docker Compose 在本地定义并运行多容器应用程序，然后再切换到 Azure 容器实例，请继续学习以下教程。

> [!div class="nextstepaction"]
> [Docker Compose 教程](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

