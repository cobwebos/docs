---
title: "Azure 容器实例教程 - 部署应用"
description: "Azure 容器实例教程 - 部署应用"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2858f20cd9da469d5983e2bef9176f5922349196
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>将容器部署到 Azure 容器实例

这是三部分教程的最后一部分。 在前几部分中，[创建了容器映像](container-instances-tutorial-prepare-app.md)并[将其推送到了 Azure 容器注册表](container-instances-tutorial-prepare-acr.md)。 本部分为教程的最后一部分内容，介绍如何将容器部署到 Azure 容器实例。 已完成的步骤包括：

> [!div class="checklist"]
> * 使用 Azure CLI 从 Azure 容器注册表部署容器
> * 在浏览器中查看应用程序
> * 查看容器日志

## <a name="before-you-begin"></a>开始之前

本教程要求运行 Azure CLI 2.0.20 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

若要完成本教程，需要 Docker 开发环境。 Docker 提供的包可在任何 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系统上轻松配置 Docker。

Azure Cloud Shell 不包含完成本教程每个步骤所需的 Docker 组件。 因此，我们建议在本地安装 Azure CLI 和 Docker 开发环境。

## <a name="deploy-the-container-using-the-azure-cli"></a>使用 Azure CLI 部署容器

使用 Azure CLI，只需单个命令即可将容器部署到 Azure 容器实例。 由于容器映像托管在专用 Azure 容器注册表中，因此必须提供访问注册表时所需的凭据。 如有必要，可按如下方式查询这些凭据。

容器注册表登录服务器（更新为注册表名）：

```azurecli
az acr show --name <acrName> --query loginServer
```

容器注册表密码：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

若要从容器注册表部署容器映像且资源请求为 1 个 CPU 核心和 1 GB 内存，请运行以下命令。 将 `<acrLoginServer>` 和 `<acrPassword>` 替换为之前两个命令获得的值。

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

将在几秒钟内收到来自 Azure 资源管理器的初始响应。 若要查看部署状态，请使用 [az container show](/cli/azure/container#az_container_show)：

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

重复 `az container show` 命令，直到状态从“挂起”更改为“正在运行”，此过程应不到 1 分钟。 当容器状态为“正在运行”时，请继续执行下一步。

## <a name="view-the-application-and-container-logs"></a>查看应用程序和容器日志

部署成功后，使用 [az container show](/cli/azure/container#az_container_show) 命令显示容器的公共 IP 地址：

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

示例输出：`"13.88.176.27"`

若要查看正在运行的应用程序，请从喜欢的浏览器中导航到此公共 IP 地址。

![浏览器中的 Hello World 应用][aci-app-browser]

还可查看容器的日志输出：

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

输出：

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要在本系列教程中创建的任何资源，可以执行 [az group delete](/cli/azure/group#delete) 命令删除资源组和其中包含的所有资源。 此命令将删除创建的容器注册表、正在运行的容器和所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程已完整演示将容器部署到 Azure 容器实例的过程。 已完成以下步骤：

> [!div class="checklist"]
> * 使用 Azure CLI 从 Azure 容器注册表部署容器
> * 在浏览器中查看应用程序
> * 查看容器日志

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
