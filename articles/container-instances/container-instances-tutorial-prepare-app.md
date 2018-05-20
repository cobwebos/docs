---
title: Azure 容器实例教程 - 准备应用
description: Azure 容器实例教程第 1 部分（共 3 部分）- 准备部署到 Azure 容器实例的应用
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 79041123196559c5759789638228ea0dd21f2762
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-container-for-deployment-to-azure-container-instances"></a>教程：创建部署到 Azure 容器实例的容器

使用 Azure 容器实例可将 Docker 容器部署到 Azure 基础结构，不需要预配任何虚拟机，也不需要采用更高级别的服务。 在本教程中，我们将一个小型 Node.js Web 应用程序打包成可以使用 Azure 容器实例运行的容器映像。

本文（本系列的第一部分）将介绍如何：

> [!div class="checklist"]
> * 克隆 GitHub 中的应用程序源代码
> * 从应用程序源创建容器映像
> * 在本地 Docker 环境中测试映像

在本教程的第二和第三部分，需要将该映像上传到 Azure 容器注册表，然后将其部署到 Azure 容器实例。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>获取应用程序代码

本教程中的示例应用程序是使用 [Node.js][nodejs] 生成的简单 Web 应用。 该应用程序提供一个静态 HTML 页面，如以下屏幕截图所示：

![显示在浏览器中的教程应用][aci-tutorial-app]

使用 Git 克隆示例应用程序的存储库：

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

也可以从 GitHub 直接[下载 ZIP 存档][aci-helloworld-zip]。

## <a name="build-the-container-image"></a>生成容器映像

示例应用程序中的 Dockerfile 演示如何生成容器。 它从基于 [Alpine Linux][alpine-linux] 的[正式 Node.js 映像][docker-hub-nodeimage]（适用于容器的小型分发版）开始演示。 然后，它将应用程序文件复制到容器中，使用 Node 包管理器安装依赖项，最后启动应用程序。

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

使用 [docker build][docker-build] 命令创建容器映像，将其标记为 *aci-tutorial-app*：

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

[docker build][docker-build] 命令的输出类似于以下内容（为方便阅读，已进行截断处理）：

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

使用 [docker images][docker-images] 命令查看已生成映像：

```bash
docker images
```

新生成的映像应会出现在列表中：

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>在本地运行容器

将容器部署到 Azure 容器实例之前，请使用 [docker run][docker-run] 在本地运行该容器，并确认它是否正常工作。 `-d` 开关可让容器在后台运行，而 `-p` 可将计算机上的任意端口映射到容器中的端口 80。

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

如果 `docker run` 命令成功，其输出会显示正在运行的容器的 ID：

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

现在，请在浏览器中导航到 http://localhost:8080，确认容器是否正在运行。 应会看到如下所示的网页：

![在浏览器中以本地方式运行应用][aci-tutorial-app-local]

## <a name="next-steps"></a>后续步骤

在本教程中，我们已创建一个可以部署到 Azure 容器实例的容器映像，并已验证它在本地运行。 到目前为止，我们已完成以下操作：

> [!div class="checklist"]
> * 从 GitHub 克隆应用程序源
> * 从应用程序源创建容器映像
> * 在本地测试容器

请转到本系列中的下一篇教程，了解如何在 Azure 容器注册表中存储容器映像：

> [!div class="nextstepaction"]
> [向 Azure 容器注册表推送映像](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
