---
title: "将 Docker 映像推送到专用 Azure 注册表 | Microsoft 文档"
description: "使用 Docker CLI 在 Azure 的专用容器注册表中推送和拉取 Docker 映像"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b6c26f28aa1e574ba3aabe53eda359cb6bf2edcc
ms.lasthandoff: 03/27/2017

---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>使用 Docker CLI 将第一个映像推送到专用 Docker 容器注册表
Azure 容器注册表存储和管理专用 [Docker](http://hub.docker.com) 容器映像，其方式类似于 [Docker Hub](https://hub.docker.com/) 存储公共 Docker 映像。 可以使用 [Docker 命令行接口](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) 对容器注册表执行[登录](https://docs.docker.com/engine/reference/commandline/login/)、[推送](https://docs.docker.com/engine/reference/commandline/push/)、[提取](https://docs.docker.com/engine/reference/commandline/pull/)和其他操作。

如需更多背景和概念，请参阅[概述](container-registry-intro.md)



## <a name="prerequisites"></a>先决条件
* **Azure 容器注册表** - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI 2.0](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 若要将本地计算机设置为 Docker 主机并访问 Docker CLI 命令，请安装 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="log-in-to-a-registry"></a>登录到注册表
运行 `docker login`，使用[注册表凭据](container-registry-authentication.md)登录到容器注册表。

以下示例传递了 Azure Active Directory [服务主体](../active-directory/active-directory-application-objects.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> 请确保指定完全限定的注册表名称（全部小写）。 在此示例中，它是 `myregistry.azurecr.io`。

## <a name="steps-to-pull-and-push-an-image"></a>提取和推送映像的步骤
以下示例从公共 Docker 中心注册表下载 Nginx 映像，为专用 Azure 容器注册表标记该映像，将其推送到注册表，然后再次拉取。

**1.提取 Nginx 的 Docker 正式映像**

首次将公共 Nginx 映像提取到本地计算机。

```
docker pull nginx
```
**2.启动 Nginx 容器**

以下命令在端口 8080 上以交互方式启动本地 Nginx 容器，方便你查看 Nginx 的输出。 正在运行的容器停止后，该命令会将它删除。

```
docker run -it --rm -p 8080:80 nginx
```

浏览到 [http://localhost:8080](http://localhost:8080) 查看正在运行的容器。 将看到类似于下面的屏幕。

![本地计算机上的 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

若要停止正在运行的容器，请按 [CTRL]+[C]。

**3.在注册表中创建映像的别名**

以下命令使用注册表的完全限定路径创建映像的别名。 此示例指定了 `samples` 命名空间，以免注册表根目录中出现混乱。

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4.将映像推送到注册表**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5.从注册表中提取映像**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6.从注册表启动 Nginx 容器**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

浏览到 [http://localhost:8080](http://localhost:8080) 查看正在运行的容器。

若要停止正在运行的容器，请按 [CTRL]+[C]。

**7.（可选）删除映像**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>并发限制
在某些情况下，并发执行调用可能导致错误。 下表包含对容器注册表进行“推送”和“拉取”操作时发生的并发调用的限制：

| 操作  | 限制                                  |
| ---------- | -------------------------------------- |
| 拉取       | 每个注册表最多 10 个并发拉取 |
| 推送       | 每个注册表最多 5 个并发推送 |

## <a name="next-steps"></a>后续步骤
了解基础知识后，便可以开始使用注册表了！ 例如，开始将容器映像部署到 [Azure 容器服务](https://azure.microsoft.com/documentation/services/container-service/)群集。

