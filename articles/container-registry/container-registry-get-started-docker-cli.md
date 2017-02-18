---
title: "Azure 容器注册表中的 Docker 映像 | Microsoft 文档"
description: "使用 Docker CLI 在 Azure 容器注册表中推送和提取 Docker 映像"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: df15eebf0052aa4713263a810df605fa1016c306

---
# <a name="push-your-first-image-to-a-container-registry-using-the-docker-cli"></a>使用 Docker CLI 将第一个映像推送到 Azure 容器注册表
Azure 容器注册表存储和管理专用 [Docker](http://hub.docker.com) 容器映像，其方式类似于 [Docker Hub](https://hub.docker.com/) 存储公共 Docker 映像。 可以使用 [Docker 命令行接口](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) 对容器注册表执行[登录](https://docs.docker.com/engine/reference/commandline/login/)、[推送](https://docs.docker.com/engine/reference/commandline/push/)、[提取](https://docs.docker.com/engine/reference/commandline/pull/)和其他操作。 

有关更多背景信息和概念，请参阅[什么是 Azure 容器注册表？](container-registry-intro.md)


> [!NOTE]
> 容器注册表目前以预览版提供。
> 
> 

## <a name="prerequisites"></a>先决条件
* **Azure 容器注册表** - 在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI 2.0 预览版](container-registry-get-started-azure-cli.md)。
* **Docker CLI** - 若要将本地计算机设置为 Docker 主机并访问 Docker CLI 命令，请安装 [Docker 引擎](https://docs.docker.com/engine/installation/)。

## <a name="log-in-to-a-registry"></a>登录到注册表
运行 `docker login`，使用[注册表凭据](container-registry-authentication.md)登录到容器注册表。

以下示例传递了 Azure Active Directory [服务主体](../active-directory/active-directory-application-objects.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。 

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> 请确保指定完全限定的注册表名称（全部小写）。 在此示例中，它是 `myregistry-contoso.azurecr.io`。

## <a name="steps-to-pull-and-push-an-image"></a>提取和推送映像的步骤
以下示例从公共 Docker 中心注册表下载 Nginx 映像，为专用 Azure 容器注册表标记该映像，将其推入到注册表，然后再次提取。

**1.提取 Nginx 的 Docker 正式映像**

首次将公共 Nginx 映像提取到本地计算机。

```
docker pull nginx
```
**2.启动 Nginx 容器**

以下命令以交互方式启动本地 Nginx 容器（以便你可以查看 Nginx 的输出）并侦听端口 8080。 正在运行的容器停止后，该命令会将它删除。

```
docker run -it --rm -p 8080:80 nginx
```

浏览到 [http://localhost:8080](http://localhost:8080) 查看正在运行的容器。 将看到类似于下面的屏幕。

![本地计算机上的 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

若要停止正在运行的容器，请按 [CTRL]+[C]。

**3.在注册表中创建映像的别名**

以下命令使用注册表的完全限定路径创建映像的别名。 此示例指定了 `samples` 命名空间，以免注册表根目录中出现混乱。

```
docker tag nginx myregistry-exp.azurecr.io/samples/nginx
```  

**4.将映像推送到注册表**

```
docker push myregistry-contoso.azurecr.io/samples/nginx
``` 

**5.从注册表中提取映像**

```
docker pull myregistry-contoso.azurecr.io/samples/nginx
``` 

**6.从注册表启动 Nginx 容器**

```
docker run -it --rm -p 8080:80 myregistry-exp.azurecr.io/samples/nginx
```

浏览到 [http://localhost:8080](http://localhost:8080) 查看正在运行的容器。

若要停止正在运行的容器，请按 [CTRL]+[C]。

**6.删除映像**

```
docker rmi myregistry-contoso.azurecr.io/samples/nginx
```



## <a name="next-steps"></a>后续步骤
了解基础知识后，便可以开始使用注册表了！ 例如，开始将容器映像部署到 [Azure 容器服务](https://azure.microsoft.com/documentation/services/container-service/)群集。






<!--HONumber=Jan17_HO4-->


