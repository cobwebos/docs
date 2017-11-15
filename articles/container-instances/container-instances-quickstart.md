---
title: "快速入门 - 创建首个 Azure 容器实例容器"
description: "Azure 容器实例部署和入门"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: dc8a94e998b36331a6a42253a68b43d76be6657c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>在 Azure 容器实例中创建你的第一个容器
使用 Azure 容器实例，可以在 Azure 中轻松创建和管理 Docker 容器，无需预配虚拟机或采用更高级别的服务。 在此快速入门中，将在 Azure 中创建容器，再通过公共 IP 地址向 Internet 公开该容器。 此操作通过单个命令完成。 短短几秒内，浏览器就会显示如下内容：

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-app-browser]

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本快速入门。 如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.20 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

Azure 容器实例是 Azure 资源，必须置于 Azure 资源组中，后者是在其中部署和管理 Azure 资源的逻辑集合。

使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>创建容器

在 [az container create][az-container-create] 命令中提供名称、Docker 映像和 Azure 资源组可创建容器。 可以选择通过公共 IP 地址向 Internet 公开容器。 在本快速入门中，需要部署一个容器，此容器托管使用 [Node.js](http://nodejs.org) 编写的小型 Web 应用。

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public
```

在数秒内就会获得请求的响应。 容器一开始处于“正在创建”状态，但会在数秒内启动。 可以使用 [az container show][az-container-show] 命令检查状态：

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

在输出底部会显示容器的预配状态及其 IP 地址：

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

在容器的状态变成“成功”后，即可在浏览器中使用所提供的 IP 地址来访问它。

![在浏览器中显示的使用 Azure 容器实例部署的应用][aci-app-browser]

## <a name="pull-the-container-logs"></a>拉取容器日志

可以使用 [az container logs][az-container-logs] 命令拉取所创建容器的日志：

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

输出：

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>删除容器

完成容器的操作后，可使用 [az container delete][az-container-delete] 命令将其删除：

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

若要验证已删除该容器，请执行 [az container list](/cli/azure/container#az_container_list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup -o table
```

mycontainer 容器不应出现在命令的输出中。 如果资源组中没有任何其他容器，则不会显示任何输出。

## <a name="next-steps"></a>后续步骤

本快速入门中使用的容器的所有代码及其 Dockerfile 均可在 [GitHub][app-github-repo] 上获取。 若要尝试使用 Azure 容器注册表来自行生成容器并将其部署到 Azure 容器实例，请继续阅读 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

若要 Azure 上尝试用于运行业务流程系统中的容器的选项，请参阅 [Service Fabric][service-fabric] 或 [Azure 容器服务 (AKS)][container-service] 快速入门。

<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
[container-service]: ../aks/kubernetes-walkthrough.md


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
