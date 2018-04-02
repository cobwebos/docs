---
title: Azure 容器实例教程 - 部署应用
description: Azure 容器实例教程第 3 部分 - 部署应用程序
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>教程：将容器部署到 Azure 容器实例

这是由三个部分组成的系列教程的最后一个教程。 在本系列教程的前几篇文章中，我们已[创建一个容器映像](container-instances-tutorial-prepare-app.md)并将其[推送到 Azure 容器注册表](container-instances-tutorial-prepare-acr.md)。 本文是本系列教程的最后一篇，介绍如何将容器部署到 Azure 容器实例。

在本教程中：

> [!div class="checklist"]
> * 将容器从 Azure 容器注册表部署到 Azure 容器实例
> * 在浏览器中查看正在运行的应用程序
> * 显示容器的日志

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>使用 Azure CLI 部署容器

在本部分，我们将使用 Azure CLI 来部署[第一篇教程](container-instances-tutorial-prepare-app.md)中生成的，并已在[第二篇教程](container-instances-tutorial-prepare-acr.md)中推送到 Azure 容器注册表的映像。 在继续之前，请务必先完成这些教程。

### <a name="get-registry-credentials"></a>获取注册表凭据

部署托管在专用容器注册表（例如[第二篇教程](container-instances-tutorial-prepare-acr.md)中创建的注册表）中的映像时，必须提供该注册表的凭据。

首先，获取容器注册表登录服务器的完整名称（请将 `<acrName>` 替换为注册表的名称）:

```azurecli
az acr show --name <acrName> --query loginServer
```

接下来，获取容器注册表密码：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>部署容器

现在，使用 [az container create][az-container-create] 部署容器。 将 `<acrLoginServer>` 和 `<acrPassword>` 替换为之前两个命令获得的值。 将 `<acrName>` 替换为容器注册表的名称。

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

将在几秒钟内收到来自 Azure 的初始响应。 在创建容器实例时所在的 Azure 区域中，`--dns-name-label` 值必须是唯一的。 如果在执行命令时收到 **DNS 名称标签**错误消息，请修改前一命令中的值。

### <a name="verify-deployment-progress"></a>检查部署进度

若要查看部署状态，请使用 [az container show][az-container-show]：

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

重复 [az container show][az-container-show] 命令，直到状态从“挂起”更改为“正在运行”为止，此过程应不到 1 分钟。 当容器状态为“正在运行”时，请继续执行下一步。

## <a name="view-the-application-and-container-logs"></a>查看应用程序和容器日志

部署成功后，使用 [az container show][az-container-show] 命令显示容器的完全限定域名 (FQDN)：

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

例如：
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

若要查看正在运行的应用程序，请从喜欢的浏览器中导航到此限定的 DNS 名称：

![浏览器中的 Hello World 应用][aci-app-browser]

还可查看容器的日志输出：

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

示例输出：

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要在本系列教程中创建的任何资源，可以执行 [az group delete][az-group-delete] 命令删除资源组和其中包含的所有资源。 此命令将删除创建的容器注册表、正在运行的容器和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程已完整演示将容器部署到 Azure 容器实例的过程。 已完成以下步骤：

> [!div class="checklist"]
> * 使用 Azure CLI 从 Azure 容器注册表部署了容器
> * 已在浏览器中查看了应用程序
> * 已查看容器日志

有了一定的基础知识后，请继续深入了解 Azure 容器实例，例如容器组的工作原理：

> [!div class="nextstepaction"]
> [Azure 容器实例中的容器组](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
